# 9.A Conectar y crear tablas

!!! tip "¿Quieres aprender SQL a fondo?"
    Esta unidad explica **lo mínimo de SQL** para entender los ejemplos. Para estudiarlo con calma (consultas, `JOIN`, agrupaciones, diseño de tablas, transacciones...) y con ejercicios, tienes la web de [SQL y bases de datos](https://apuntes-dam.github.io/sql-apuntes/).

## Qué es una base de datos relacional

Una **base de datos relacional** guarda la información en **tablas**: cada tabla tiene **columnas** (los datos que se guardan) y **filas** (cada elemento guardado). Las tablas se **relacionan** entre sí mediante claves, y todo se maneja con un lenguaje común, **SQL**.

| Concepto | Qué es | Ejemplo |
|---|---|---|
| **Tabla** | Un conjunto de datos del mismo tipo | `libros` |
| **Fila** (registro) | Un elemento guardado | el libro «Don Quijote» |
| **Columna** (campo) | Un dato de cada fila, con un tipo | `titulo`, `anio`, `stock` |
| **Clave primaria** (*primary key*) | Columna que **identifica de forma única** cada fila | `id` |
| **Clave foránea** (*foreign key*) | Columna que **apunta a la clave primaria de otra tabla** | `libros.id_autor` → `autores.id` |

En los ejemplos de esta unidad hay tres tablas relacionadas: un **autor** tiene muchos **libros** y un **libro** puede tener muchos **préstamos**.

```text
autores (id, nombre)  1 ──── N  libros (id, titulo, anio, stock, id_autor)  1 ──── N  prestamos (id, id_libro, socio)
```

## Motores de bases de datos

| Motor | Cómo funciona | Cuándo se usa |
|---|---|---|
| **SQLite** | Un **archivo** (o la memoria); no hay servidor ni instalación | Aprender, apps móviles y de escritorio, pruebas |
| **H2**, HSQLDB | Base de datos **embebida en Java** (archivo o memoria) | Pruebas y programas Java sencillos |
| **MySQL**, **PostgreSQL** | **Servidor** al que se conecta por red con usuario y contraseña | Aplicaciones reales con muchos usuarios |

!!! note "Qué motor usan estos ejemplos"
    Todos los ejemplos usan **SQLite**, porque no necesita instalar nada. Los ejercicios proponen **H2** para Java y Kotlin: el código JDBC es el mismo, solo cambian la **dependencia** (`com.h2database:h2`) y la **URL** (`jdbc:h2:./data/tienda`). Yo he comprobado los ejemplos con SQLite, no con H2. Las sentencias también varían un poco entre motores: por ejemplo, la columna que se numera sola es `AUTOINCREMENT` en SQLite y `AUTO_INCREMENT` en MySQL y H2.

## Conectar, crear las tablas e insertar datos

Java accede a las bases de datos con **JDBC** (`java.sql`), una API común para todos los motores. Cada motor aporta su **controlador** (*driver*) en un archivo `.jar` que hay que tener en el *classpath*; estos ejemplos usan el de SQLite (`org.xerial:sqlite-jdbc`). `DriverManager.getConnection(url)` abre la conexión (`jdbc:sqlite:biblioteca.db` para un archivo, `jdbc:sqlite::memory:` para memoria). Se lanzan sentencias con `Statement` (SQL fijo) o `PreparedStatement` (con parámetros `?`). Todo es `AutoCloseable`, así que se cierra con **`try-with-resources`**. Los errores son una **`SQLException`**.

```java
import java.io.File;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class Bd1 {
    public static void main(String[] args) {
        try (Connection con = DriverManager.getConnection("jdbc:sqlite:biblioteca.db")) {
            System.out.println("conectado a la base de datos");
            try (Statement st = con.createStatement()) {
                st.execute("CREATE TABLE autores (id INTEGER PRIMARY KEY AUTOINCREMENT, nombre TEXT NOT NULL)");
                st.execute("CREATE TABLE libros (id INTEGER PRIMARY KEY AUTOINCREMENT, titulo TEXT NOT NULL, "
                        + "anio INTEGER, stock INTEGER NOT NULL, id_autor INTEGER NOT NULL REFERENCES autores(id))");
                st.execute("CREATE TABLE prestamos (id INTEGER PRIMARY KEY AUTOINCREMENT, "
                        + "id_libro INTEGER NOT NULL REFERENCES libros(id), socio TEXT NOT NULL)");
            }
            System.out.println("tablas creadas");

            String[] autores = {"Cervantes", "García Márquez"};
            try (PreparedStatement ps = con.prepareStatement("INSERT INTO autores (nombre) VALUES (?)")) {
                for (String nombre : autores) {
                    ps.setString(1, nombre);
                    ps.executeUpdate();
                }
            }
            System.out.println("autores insertados: " + autores.length);

            Object[][] libros = {
                {"Don Quijote", 1605, 3, 1},
                {"Novelas ejemplares", 1613, 2, 1},
                {"Cien años de soledad", 1967, 5, 2},
                {"El coronel no tiene quien le escriba", 1961, 1, 2},
            };
            String insertar = "INSERT INTO libros (titulo, anio, stock, id_autor) VALUES (?, ?, ?, ?)";
            try (PreparedStatement ps = con.prepareStatement(insertar)) {
                for (Object[] libro : libros) {
                    ps.setString(1, (String) libro[0]);
                    ps.setInt(2, (int) libro[1]);
                    ps.setInt(3, (int) libro[2]);
                    ps.setInt(4, (int) libro[3]);
                    ps.executeUpdate();
                }
            }
            System.out.println("libros insertados: " + libros.length);

            try (Statement st = con.createStatement(); ResultSet rs = st.executeQuery("SELECT COUNT(*) FROM libros")) {
                rs.next();
                System.out.println("libros en la base de datos: " + rs.getInt(1));
            }

            try (Statement st = con.createStatement()) {
                st.execute("INSERT INTO editoriales (nombre) VALUES ('X')");
            } catch (SQLException e) {
                System.out.println("Error controlado: la sentencia no es válida");
            }
        } catch (SQLException e) {
            System.out.println("Error de base de datos: " + e.getMessage());
        }
        System.out.println("conexión cerrada");

        if (new File("biblioteca.db").delete()) {
            System.out.println("archivo biblioteca.db borrado");
        }
    }
}
```

Salida:

```text
conectado a la base de datos
tablas creadas
autores insertados: 2
libros insertados: 4
libros en la base de datos: 4
Error controlado: la sentencia no es válida
conexión cerrada
archivo biblioteca.db borrado
```

Qué hace cada paso:

1. **Conecta** con la base de datos (el archivo `biblioteca.db`, que se crea si no existe).
2. **Crea las tres tablas** con `CREATE TABLE`, indicando para cada columna su tipo y sus restricciones.
3. **Inserta datos** con `INSERT` y **parámetros** (`?`): los valores no se pegan dentro del texto SQL, se pasan aparte. Esto es lo que se explica en [9.C](t-buenas-practicas.md) y es **obligatorio** cuando el dato viene del usuario.
4. **Cuenta** las filas con una consulta.
5. **Provoca un error a propósito** (insertar en una tabla que no existe) y lo **captura**: el programa no se detiene y muestra un mensaje claro.
6. **Cierra la conexión** (el `try-with-resources` de la conexión) **siempre**, haya habido error o no. Solo después se puede borrar el archivo: con la conexión abierta, Windows no permite borrarlo.

## Crear tablas: tipos y restricciones

```sql
CREATE TABLE libros (
  id       INTEGER PRIMARY KEY AUTOINCREMENT,   -- identifica cada fila; se numera solo
  titulo   TEXT    NOT NULL,                    -- obligatorio
  anio     INTEGER,                             -- puede quedar vacío (NULL)
  stock    INTEGER NOT NULL,
  id_autor INTEGER NOT NULL REFERENCES autores(id)   -- clave foránea
);
```

| Restricción | Significa |
|---|---|
| `PRIMARY KEY` | Identifica la fila; no se repite ni puede ser nula |
| `NOT NULL` | La columna no puede quedar vacía |
| `UNIQUE` | No puede haber dos filas con el mismo valor (por ejemplo, un correo) |
| `REFERENCES tabla(columna)` | Clave foránea: el valor **debe existir** en la otra tabla |
| `DEFAULT valor` | Valor que se usa si no se indica ninguno |

| Tipo en SQLite | Para qué | Equivalente habitual en otros motores |
|---|---|---|
| `INTEGER` | Números enteros | `INT`, `BIGINT` |
| `TEXT` | Texto | `VARCHAR(n)`, `TEXT` |
| `REAL` | Números decimales | `DOUBLE`, `FLOAT` |
| `BLOB` | Datos binarios | `BLOB`, `BYTEA` |

!!! tip "Dinero y decimales"
    Para importes, otros motores ofrecen `DECIMAL(10,2)`, que guarda decimales **exactos**. Con `REAL` (coma flotante) pueden aparecer errores de redondeo, como en `0.1 + 0.2`. Una alternativa muy usada es guardar el importe **en céntimos**, como entero.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| No cerrar la conexión | `finally` / `try-with-resources` / `use` |
| Pegar el valor dentro del SQL | Usar parámetros `?` |
| Olvidar confirmar los cambios (en Python, `commit`) | Confirmar al terminar o usar una transacción ([9.C](t-buenas-practicas.md)) |
| Crear una tabla que ya existe | Crear las tablas una vez, o usar `CREATE TABLE IF NOT EXISTS` |
| Insertar con claves foráneas que no existen | Insertar primero la tabla «padre» (los autores) y luego la «hija» (los libros) |

## Para practicar

Haz los ejercicios de [U9.1 · Conexión y creación de tablas](conexion.md). [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) compara cómo se escribe en cada lenguaje.
