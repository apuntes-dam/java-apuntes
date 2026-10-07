# 9.B Consultas, modificaciones y borrados

## Las cuatro sentencias básicas

| Operación | Sentencia | Ejemplo |
|---|---|---|
| **Leer** | `SELECT` | `SELECT titulo FROM libros WHERE anio < 1700` |
| **Insertar** | `INSERT` | `INSERT INTO autores (nombre) VALUES ('Cervantes')` |
| **Modificar** | `UPDATE` | `UPDATE libros SET stock = 10 WHERE id = 1` |
| **Borrar** | `DELETE` | `DELETE FROM libros WHERE id = 4` |

!!! danger "Sin `WHERE`, afecta a TODAS las filas"
    `UPDATE libros SET stock = 0` deja **todos** los libros sin stock, y `DELETE FROM libros` **vacía la tabla entera**. Escribe siempre el `WHERE` primero y, antes de ejecutar un `UPDATE` o un `DELETE`, prueba su `WHERE` con un `SELECT` para ver qué filas afectará.

## Consultar: `SELECT`

Un `SELECT` se construye por partes, siempre en este orden:

```sql
SELECT   columnas            -- qué quiero ver
FROM     tabla               -- de dónde
JOIN     otra ON condición   -- (opcional) uniendo con otra tabla
WHERE    condición           -- (opcional) qué filas
GROUP BY columna             -- (opcional) agrupar
ORDER BY columna             -- (opcional) ordenar
```

| Idea | Ejemplo |
|---|---|
| Filtrar | `WHERE anio < 1700 AND stock > 0` |
| Ordenar | `ORDER BY anio DESC` (descendente) |
| Unir tablas (**JOIN**) | `FROM libros l JOIN autores a ON a.id = l.id_autor` |
| Contar, sumar, promediar | `COUNT(*)`, `SUM(stock)`, `AVG(anio)`, `MIN(...)`, `MAX(...)` |
| Agrupar | `GROUP BY a.nombre` (una fila por autor) |

Un **`JOIN`** combina filas de dos tablas **relacionadas por una clave**: aquí, cada libro con el nombre de su autor. Sin el `JOIN` solo verías el número `id_autor`.

## Un ejemplo completo

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class Bd2 {
    static void preparar(Connection con) throws SQLException {
        try (Statement st = con.createStatement()) {
            st.execute("PRAGMA foreign_keys = ON"); // SQLite no comprueba las claves foráneas si no se activa
            st.execute("CREATE TABLE autores (id INTEGER PRIMARY KEY AUTOINCREMENT, nombre TEXT NOT NULL)");
            st.execute("CREATE TABLE libros (id INTEGER PRIMARY KEY AUTOINCREMENT, titulo TEXT NOT NULL, anio INTEGER, "
                    + "stock INTEGER NOT NULL, id_autor INTEGER NOT NULL REFERENCES autores(id))");
            st.execute("INSERT INTO autores (nombre) VALUES ('Cervantes'), ('García Márquez')");
            st.execute("INSERT INTO libros (titulo, anio, stock, id_autor) VALUES "
                    + "('Don Quijote', 1605, 3, 1), ('Novelas ejemplares', 1613, 2, 1), "
                    + "('Cien años de soledad', 1967, 5, 2), ('El coronel no tiene quien le escriba', 1961, 1, 2)");
        }
    }

    static int entero(Connection con, String sql) throws SQLException {
        try (Statement st = con.createStatement(); ResultSet rs = st.executeQuery(sql)) {
            rs.next();
            return rs.getInt(1);
        }
    }

    public static void main(String[] args) throws SQLException {
        try (Connection con = DriverManager.getConnection("jdbc:sqlite::memory:")) {
            preparar(con);

            System.out.println("antes de 1700:");
            String consulta = "SELECT a.nombre, l.titulo FROM libros l JOIN autores a ON a.id = l.id_autor "
                    + "WHERE l.anio < ? ORDER BY l.anio";
            try (PreparedStatement ps = con.prepareStatement(consulta)) {
                ps.setInt(1, 1700);
                try (ResultSet rs = ps.executeQuery()) {
                    while (rs.next()) {
                        System.out.println("  " + rs.getString(1) + " — " + rs.getString(2));
                    }
                }
            }

            System.out.println("libros por autor:");
            consulta = "SELECT a.nombre, COUNT(*) FROM libros l JOIN autores a ON a.id = l.id_autor "
                    + "GROUP BY a.nombre ORDER BY a.nombre";
            try (Statement st = con.createStatement(); ResultSet rs = st.executeQuery(consulta)) {
                while (rs.next()) {
                    System.out.println("  " + rs.getString(1) + ": " + rs.getInt(2));
                }
            }

            System.out.println("stock total: " + entero(con, "SELECT SUM(stock) FROM libros"));

            try (PreparedStatement ps = con.prepareStatement("UPDATE libros SET stock = ? WHERE titulo = ?")) {
                ps.setInt(1, 10);
                ps.setString(2, "Don Quijote");
                System.out.println("filas modificadas: " + ps.executeUpdate());
            }
            System.out.println("stock total tras reponer: " + entero(con, "SELECT SUM(stock) FROM libros"));

            try (Statement st = con.createStatement()) {
                st.execute("DELETE FROM autores WHERE id = 1");
            } catch (SQLException e) {
                System.out.println("borrar el autor 1: no se puede, tiene libros");
            }

            try (PreparedStatement ps = con.prepareStatement("DELETE FROM libros WHERE id = ?")) {
                ps.setInt(1, 4);
                System.out.println("filas borradas: " + ps.executeUpdate());
            }
            System.out.println("libros que quedan: " + entero(con, "SELECT COUNT(*) FROM libros"));
        }
    }
}
```

Salida:

```text
antes de 1700:
  Cervantes — Don Quijote
  Cervantes — Novelas ejemplares
libros por autor:
  Cervantes: 2
  García Márquez: 2
stock total: 11
filas modificadas: 1
stock total tras reponer: 18
borrar el autor 1: no se puede, tiene libros
filas borradas: 1
libros que quedan: 3
```

Qué enseña este programa:

* **Consulta con unión y parámetro**: los libros anteriores a 1700 con el nombre de su autor, ordenados por año.
* **Agrupación**: cuántos libros tiene cada autor (`GROUP BY` con `COUNT`).
* **Un valor suelto**: la suma del stock con `SUM`.
* **`UPDATE`**: repone el stock de un libro; el programa muestra **cuántas filas** se han modificado (si fuera 0, el `WHERE` no encontró nada).
* **Una restricción que protege los datos**: borrar el autor 1 **falla**, porque todavía tiene libros. La base de datos impide dejar libros apuntando a un autor que ya no existe.
* **`DELETE`**: borra un libro concreto con su `WHERE`.

## Leer los resultados en Java

| Necesito... | En Java |
|---|---|
| Ejecutar una consulta y recorrer las filas | `ps.executeQuery()` y `while (rs.next()) { ... }` |
| Leer una columna de una fila | `rs.getString(1)`, `rs.getInt(2)`, o por nombre: `rs.getString("titulo")`. **Las posiciones empiezan en 1**, no en 0 |
| Sentencias que modifican (`INSERT`, `UPDATE`, `DELETE`) | `ps.executeUpdate()` |
| Cuántas filas se han modificado | es lo que **devuelve** `executeUpdate()` |
| Un único valor | `rs.next(); rs.getInt(1)` |

## Borrar datos relacionados

En SQLite las claves foráneas **no se comprueban** salvo que se active con `PRAGMA foreign_keys = ON` en cada conexión, como hace el ejemplo. La mayoría de los demás motores (MySQL, PostgreSQL, H2) las comprueban siempre. El error es una `SQLException`.

Si intentas borrar una fila a la que **otras filas apuntan**, la base de datos rechaza la operación. Hay tres formas de resolverlo:

| Estrategia | Cómo | Cuándo |
|---|---|---|
| **Borrar primero lo dependiente** | `DELETE` de los libros del autor y después `DELETE` del autor | Lo más claro y explícito |
| **Borrado en cascada** | Declarar la clave con `ON DELETE CASCADE`: al borrar el autor se borran solos sus libros | Cuando los datos hijos no tienen sentido sin el padre. **Peligroso** si se usa sin pensar |
| **No permitir borrar** | Dejar el error y avisar al usuario | Cuando el borrado indica un fallo de lógica |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| `UPDATE` o `DELETE` sin `WHERE` | Escribir el `WHERE` antes que nada y probarlo con un `SELECT` |
| Esperar un orden sin `ORDER BY` | Sin él, el orden **no está garantizado** |
| Contar mal columnas en JDBC | Empiezan en **1** |
| Intentar borrar un registro del que dependen otros | Borrar antes lo dependiente, o usar cascada con cuidado |
| Pegar valores dentro del SQL | Parámetros `?` (ver [9.C](t-buenas-practicas.md)) |

## Para practicar

Haz los ejercicios de [U9.2 · Consultas, borrados y modificaciones](consultas.md). Fíjate en las estrategias de borrado de la tabla anterior cuando llegues al ejercicio de las eliminaciones. [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) compara el código de cada lenguaje.
