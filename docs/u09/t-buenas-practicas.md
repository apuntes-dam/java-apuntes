# 9.C Buenas prácticas: pool, seguridad, transacciones y DAO

## Pool de conexiones

Abrir una conexión con un servidor de bases de datos es **lento y costoso** (red, autenticación). Un programa que abre y cierra una conexión por cada operación desperdicia mucho tiempo. Un **pool de conexiones** mantiene varias conexiones **ya abiertas** y las **presta** a quien las necesita:

```text
programa ──pide conexión──▶  [ pool: 🔌 🔌 🔌 🔌 ]  ──▶  servidor de base de datos
         ◀──la devuelve───
```

En Java el pool más usado es **HikariCP**: se configura una vez y devuelve `Connection` ya abiertas con `dataSource.getConnection()`. Cuando se «cierra» una de esas conexiones, en realidad **vuelve al pool** en lugar de cerrarse de verdad. Con SQLite o H2 embebidos no hace falta.

## Seguridad: la inyección SQL

La **inyección SQL** ocurre cuando se construye una consulta **pegando texto del usuario** dentro del SQL. Si el usuario escribe SQL en lugar de un dato normal, **cambia el significado de la consulta**: puede ver datos que no debe, saltarse un inicio de sesión o borrar tablas. Es una de las vulnerabilidades más graves y frecuentes de las aplicaciones reales.

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

public class Bd3 {
    static void preparar(Connection con) throws SQLException {
        try (Statement st = con.createStatement()) {
            st.execute("CREATE TABLE libros (id INTEGER PRIMARY KEY AUTOINCREMENT, titulo TEXT NOT NULL, stock INTEGER NOT NULL)");
            st.execute("INSERT INTO libros (titulo, stock) VALUES ('Don Quijote', 3), ('Novelas ejemplares', 2), "
                    + "('Cien años de soledad', 5), ('El coronel no tiene quien le escriba', 1)");
        }
    }

    static List<String> buscarInseguro(Connection con, String titulo) throws SQLException {
        String sql = "SELECT titulo FROM libros WHERE titulo = '" + titulo + "'"; // ¡NUNCA así!
        List<String> resultado = new ArrayList<>();
        try (Statement st = con.createStatement(); ResultSet rs = st.executeQuery(sql)) {
            while (rs.next()) {
                resultado.add(rs.getString(1));
            }
        }
        return resultado;
    }

    static List<String> buscarSeguro(Connection con, String titulo) throws SQLException {
        List<String> resultado = new ArrayList<>();
        try (PreparedStatement ps = con.prepareStatement("SELECT titulo FROM libros WHERE titulo = ?")) {
            ps.setString(1, titulo);
            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    resultado.add(rs.getString(1));
                }
            }
        }
        return resultado;
    }

    public static void main(String[] args) throws SQLException {
        try (Connection con = DriverManager.getConnection("jdbc:sqlite::memory:")) {
            preparar(con);
            String maliciosa = "x' OR '1'='1";
            System.out.println("[inseguro] Don Quijote -> " + buscarInseguro(con, "Don Quijote").size() + " resultado(s)");
            System.out.println("[inseguro] " + maliciosa + " -> " + buscarInseguro(con, maliciosa).size() + " resultado(s)");
            System.out.println("[seguro] Don Quijote -> " + buscarSeguro(con, "Don Quijote").size() + " resultado(s)");
            System.out.println("[seguro] " + maliciosa + " -> " + buscarSeguro(con, maliciosa).size() + " resultado(s)");
        }
    }
}
```

Salida:

```text
[inseguro] Don Quijote -> 1 resultado(s)
[inseguro] x' OR '1'='1 -> 4 resultado(s)
[seguro] Don Quijote -> 1 resultado(s)
[seguro] x' OR '1'='1 -> 0 resultado(s)
```

La cadena `x' OR '1'='1` hace que la consulta insegura quede como:

```sql
SELECT titulo FROM libros WHERE titulo = 'x' OR '1'='1'
```

Como `'1'='1'` siempre es cierto, la consulta **devuelve todos los libros**. Con una **consulta parametrizada**, esa misma cadena se trata como un **título que no existe**, porque el valor viaja **aparte del SQL** y nunca se interpreta como código.

`PreparedStatement ps = con.prepareStatement("... WHERE titulo = ?"); ps.setString(1, titulo);`: los valores se asignan con `setString`, `setInt`... (la primera posición es 1).

!!! danger "Regla de oro"
    **Nunca** construyas SQL concatenando o interpolando datos que no controles (todo lo que escribe el usuario, lo que llega de un formulario o de la red). Usa **siempre** parámetros. Un parámetro sirve para **valores**; los nombres de tablas o columnas no se pueden parametrizar: si deben variar, elígelos de una lista fija de opciones permitidas.

## Transacciones

Una **transacción** agrupa varias operaciones para que se ejecuten **como una sola**: o se hacen **todas** o no se hace **ninguna**. Es imprescindible cuando un cambio requiere varios pasos. Prestar un libro, por ejemplo, exige **registrar el préstamo** y **descontar el stock**: si lo primero se hace y lo segundo falla, la base de datos quedaría incoherente.

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class Bd4 {
    static class PrestamoFallido extends Exception {
        PrestamoFallido(String mensaje) {
            super(mensaje);
        }
    }

    static void preparar(Connection con) throws SQLException {
        try (Statement st = con.createStatement()) {
            st.execute("CREATE TABLE libros (id INTEGER PRIMARY KEY AUTOINCREMENT, titulo TEXT NOT NULL, stock INTEGER NOT NULL)");
            st.execute("CREATE TABLE prestamos (id INTEGER PRIMARY KEY AUTOINCREMENT, id_libro INTEGER NOT NULL, socio TEXT NOT NULL)");
            st.execute("INSERT INTO libros (titulo, stock) VALUES ('Don Quijote', 3), ('Novelas ejemplares', 2), "
                    + "('Cien años de soledad', 5), ('El coronel no tiene quien le escriba', 1)");
        }
    }

    static int entero(Connection con, String sql, int parametro) throws SQLException {
        try (PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setInt(1, parametro);
            try (ResultSet rs = ps.executeQuery()) {
                rs.next();
                return rs.getInt(1);
            }
        }
    }

    /** Registra el préstamo y descuenta el stock como UNA sola operación. */
    static String prestar(Connection con, int idLibro, String socio, boolean fallarAMitad) throws SQLException {
        con.setAutoCommit(false); // empieza la transacción
        try {
            if (entero(con, "SELECT stock FROM libros WHERE id = ?", idLibro) < 1) {
                throw new PrestamoFallido("no hay stock");
            }
            try (PreparedStatement ps = con.prepareStatement("INSERT INTO prestamos (id_libro, socio) VALUES (?, ?)")) {
                ps.setInt(1, idLibro);
                ps.setString(2, socio);
                ps.executeUpdate();
            }
            if (fallarAMitad) {
                throw new PrestamoFallido("error simulado a mitad de la operación");
            }
            try (PreparedStatement ps = con.prepareStatement("UPDATE libros SET stock = stock - 1 WHERE id = ?")) {
                ps.setInt(1, idLibro);
                ps.executeUpdate();
            }
            con.commit(); // todo ha ido bien: se confirma
            return "correcto";
        } catch (PrestamoFallido e) {
            con.rollback(); // algo ha fallado: se deshace TODO
            return e.getMessage();
        } finally {
            con.setAutoCommit(true);
        }
    }

    public static void main(String[] args) throws SQLException {
        try (Connection con = DriverManager.getConnection("jdbc:sqlite::memory:")) {
            preparar(con);
            System.out.println("préstamo 1: " + prestar(con, 4, "Ana", false));
            System.out.println("préstamo 2: " + prestar(con, 4, "Luis", false));
            System.out.println("préstamo 3: " + prestar(con, 3, "Eva", true));
            try (Statement st = con.createStatement(); ResultSet rs = st.executeQuery("SELECT COUNT(*) FROM prestamos")) {
                rs.next();
                System.out.println("préstamos registrados: " + rs.getInt(1));
            }
            System.out.println("stock del libro 4: " + entero(con, "SELECT stock FROM libros WHERE id = ?", 4));
            System.out.println("stock del libro 3: " + entero(con, "SELECT stock FROM libros WHERE id = ?", 3));
        }
    }
}
```

Salida:

```text
préstamo 1: correcto
préstamo 2: no hay stock
préstamo 3: error simulado a mitad de la operación
préstamos registrados: 1
stock del libro 4: 0
stock del libro 3: 5
```

El tercer préstamo falla **a propósito, después de haber insertado el préstamo**. Gracias a la transacción, el `ROLLBACK` **deshace también esa inserción**: al final solo hay **un** préstamo registrado y el stock del libro 3 sigue en 5. Sin transacción, habría un préstamo «fantasma» sin descontar.

En JDBC, por defecto cada sentencia se confirma sola (*autocommit*). Para agrupar varias, se desactiva con `con.setAutoCommit(false)`, y se termina con `con.commit()` (confirmar) o `con.rollback()` (deshacer). Hay que **volver a activar** el autocommit al terminar, como hace el ejemplo.

Una transacción cumple las propiedades **ACID**:

| Propiedad | Significa |
|---|---|
| **A**tomicidad | Todo o nada |
| **C**onsistencia | Los datos siguen cumpliendo las reglas (claves, restricciones) |
| **I**slamiento | Varias operaciones simultáneas no se estorban |
| **D**urabilidad | Lo confirmado queda guardado aunque falle el equipo |

## El patrón DAO

Si el SQL está **repartido por todo el programa**, cualquier cambio en las tablas obliga a buscarlo en cien sitios, y es imposible probar la lógica sin una base de datos. El patrón **DAO** (*Data Access Object*) lo soluciona: **una clase** concentra **todo** el acceso a los datos de una tabla, y el resto del programa solo habla con ella y con **objetos**, nunca con conexiones ni con SQL.

```text
  Programa  ──▶  Servicio (reglas)  ──▶  DAO (SQL)  ──▶  Base de datos
  objetos Libro        usa objetos            convierte filas ↔ objetos
```

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

record Libro(Integer id, String titulo, int anio, int stock, int idAutor) {}

/** Único sitio del programa que conoce el SQL y la conexión. */
class LibroDao {
    private final Connection con;

    LibroDao(Connection con) {
        this.con = con;
    }

    private static Libro deFila(ResultSet rs) throws SQLException {
        return new Libro(rs.getInt(1), rs.getString(2), rs.getInt(3), rs.getInt(4), rs.getInt(5));
    }

    Libro guardar(Libro libro) throws SQLException {
        try (PreparedStatement ps = con.prepareStatement(
                "INSERT INTO libros (titulo, anio, stock, id_autor) VALUES (?, ?, ?, ?)", Statement.RETURN_GENERATED_KEYS)) {
            ps.setString(1, libro.titulo());
            ps.setInt(2, libro.anio());
            ps.setInt(3, libro.stock());
            ps.setInt(4, libro.idAutor());
            ps.executeUpdate();
            try (ResultSet claves = ps.getGeneratedKeys()) {
                claves.next();
                return new Libro(claves.getInt(1), libro.titulo(), libro.anio(), libro.stock(), libro.idAutor());
            }
        }
    }

    Libro buscar(int id) throws SQLException {
        try (PreparedStatement ps = con.prepareStatement("SELECT id, titulo, anio, stock, id_autor FROM libros WHERE id = ?")) {
            ps.setInt(1, id);
            try (ResultSet rs = ps.executeQuery()) {
                return rs.next() ? deFila(rs) : null;
            }
        }
    }

    List<Libro> todos() throws SQLException {
        List<Libro> lista = new ArrayList<>();
        try (Statement st = con.createStatement();
             ResultSet rs = st.executeQuery("SELECT id, titulo, anio, stock, id_autor FROM libros ORDER BY id")) {
            while (rs.next()) {
                lista.add(deFila(rs));
            }
        }
        return lista;
    }

    List<Libro> porAutor(String nombre) throws SQLException {
        List<Libro> lista = new ArrayList<>();
        String consulta = "SELECT l.id, l.titulo, l.anio, l.stock, l.id_autor FROM libros l "
                + "JOIN autores a ON a.id = l.id_autor WHERE a.nombre = ? ORDER BY l.anio";
        try (PreparedStatement ps = con.prepareStatement(consulta)) {
            ps.setString(1, nombre);
            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    lista.add(deFila(rs));
                }
            }
        }
        return lista;
    }

    void cambiarStock(int id, int diferencia) throws SQLException {
        try (PreparedStatement ps = con.prepareStatement("UPDATE libros SET stock = stock + ? WHERE id = ?")) {
            ps.setInt(1, diferencia);
            ps.setInt(2, id);
            ps.executeUpdate();
        }
    }
}

/** Servicio: usa el DAO y no contiene SQL. */
class Catalogo {
    private final LibroDao dao;

    Catalogo(LibroDao dao) {
        this.dao = dao;
    }

    void prestarUno(int id) throws SQLException {
        Libro libro = dao.buscar(id);
        if (libro == null) {
            throw new IllegalArgumentException("el libro no existe");
        }
        if (libro.stock() < 1) {
            throw new IllegalStateException("no hay stock");
        }
        dao.cambiarStock(id, -1);
    }
}

public class Bd5 {
    static String texto(Libro l) {
        return "Libro(id=" + l.id() + ", titulo=" + l.titulo() + ", anio=" + l.anio() + ", stock=" + l.stock() + ")";
    }

    static void preparar(Connection con) throws SQLException {
        try (Statement st = con.createStatement()) {
            st.execute("CREATE TABLE autores (id INTEGER PRIMARY KEY AUTOINCREMENT, nombre TEXT NOT NULL)");
            st.execute("CREATE TABLE libros (id INTEGER PRIMARY KEY AUTOINCREMENT, titulo TEXT NOT NULL, anio INTEGER, "
                    + "stock INTEGER NOT NULL, id_autor INTEGER NOT NULL REFERENCES autores(id))");
            st.execute("INSERT INTO autores (nombre) VALUES ('Cervantes'), ('García Márquez'), ('Frank Herbert')");
            st.execute("INSERT INTO libros (titulo, anio, stock, id_autor) VALUES "
                    + "('Don Quijote', 1605, 3, 1), ('Novelas ejemplares', 1613, 2, 1), "
                    + "('Cien años de soledad', 1967, 5, 2), ('El coronel no tiene quien le escriba', 1961, 1, 2)");
        }
    }

    public static void main(String[] args) throws SQLException {
        try (Connection con = DriverManager.getConnection("jdbc:sqlite::memory:")) {
            preparar(con);
            LibroDao dao = new LibroDao(con);
            Catalogo catalogo = new Catalogo(dao);

            Libro nuevo = dao.guardar(new Libro(null, "Dune", 1965, 2, 3));
            System.out.println("guardado: " + texto(nuevo));
            System.out.println("buscar(5): " + dao.buscar(5).titulo());
            System.out.println("buscar(99): " + (dao.buscar(99) == null ? "no existe" : "existe"));
            System.out.println("del autor Cervantes: " + dao.porAutor("Cervantes").stream()
                    .map(Libro::titulo).collect(Collectors.joining(", ")));
            catalogo.prestarUno(5);
            System.out.println("stock de Dune tras prestar uno: " + dao.buscar(5).stock());
            System.out.println("total de libros: " + dao.todos().size());
        }
    }
}
```

Salida:

```text
guardado: Libro(id=5, titulo=Dune, anio=1965, stock=2)
buscar(5): Dune
buscar(99): no existe
del autor Cervantes: Don Quijote, Novelas ejemplares
stock de Dune tras prestar uno: 1
total de libros: 5
```

Fíjate en el reparto de responsabilidades:

* **`Libro`** es un objeto de datos, sin SQL.
* **`LibroDao`** es el **único** sitio con SQL y con la conexión. Convierte filas en objetos `Libro` y al revés.
* **`Catalogo`** es el servicio: contiene las **reglas** («no se puede prestar sin stock») y usa el DAO, pero **no contiene SQL**.
* Si mañana cambia el motor de base de datos, solo se modifica el DAO.

!!! tip "Relación con SOLID"
    El DAO aplica la **responsabilidad única** (cada clase tiene una sola razón para cambiar) y, si el servicio recibe el DAO por el constructor, la **inversión de dependencias** (ver [6.B](../u06/t-solid-1.md) y [6.C](../u06/t-solid-2.md)): el servicio se podría probar con un DAO falso, sin base de datos.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Concatenar texto del usuario en el SQL | Parámetros `?`, siempre |
| Varias operaciones relacionadas sin transacción | Agruparlas con `BEGIN`/`COMMIT` y deshacer ante cualquier error |
| Olvidar el `ROLLBACK` cuando algo falla | Capturar el error y deshacer siempre |
| SQL esparcido por todo el programa | Una capa DAO |
| Abrir una conexión por cada operación contra un servidor | Un pool, o una conexión compartida si es SQLite |

## Para practicar

Haz los ejercicios de [U9.3 · Pool, seguridad, transacciones y DAO](buenas-practicas.md). Para ver cómo se escribe en otro lenguaje: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
