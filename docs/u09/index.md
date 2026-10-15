# U9 · Bases de datos relacionales

<div class="narrador" data-slug="u09-index" data-voces="mujer:Mujer,hombre:Hombre,hombre2:Hombre 2" data-titulo="Resumen de la unidad 9"></div>

??? note "Leer el resumen"
    Los datos importantes no se pueden perder al cerrar el programa, y por eso se guardan en una base de datos relacional.
    Aprenderás a conectarte a ella desde el código, a ejecutar sentencias y consultas, y a agrupar varias operaciones en una transacción, para que se hagan todas o ninguna.
    También verás cómo evitar los ataques de inyección de SQL, y el patrón DAO, que separa el código que habla con la base de datos del resto del programa.


Guardar datos en una **base de datos relacional** desde el código: conexión, sentencias, consultas, transacciones, seguridad y el patrón DAO.

## Teoría

* [9.A Conectar y crear tablas](t-conexion.md): tablas, claves, motores, conexión y `CREATE TABLE`.
* [9.B Consultas y modificaciones](t-consultas.md): `SELECT`, `JOIN`, `UPDATE`, `DELETE` y claves foráneas.
* [9.C Buenas prácticas](t-buenas-practicas.md): pool, inyección SQL, transacciones y patrón DAO.

## Antes de empezar: qué debes dominar

* Bases de datos relacionales y SQL básico (`SELECT`, `INSERT`, `UPDATE`, `DELETE`, `JOIN`).
* Conexión y cierre de recursos.
* Sentencias parametrizadas frente a concatenar SQL.
* Transacciones: `commit` y `rollback`.
* Capa de acceso a datos (DAO).

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [U9.1 · Conexión y creación de tablas](conexion.md) | 2 |
| [U9.2 · Consultas, borrados y modificaciones](consultas.md) | 3 |
| [U9.3 · Pool, seguridad, transacciones y DAO](buenas-practicas.md) | 4 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u09"></div>
