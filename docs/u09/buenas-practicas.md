# U9.3 · Pool, seguridad, transacciones y DAO

<div class="ej-gate" data-unit="u09" data-nombre="U9 · Bases de datos relacionales"></div>

Buenas prácticas de acceso a datos: reutilizar conexiones, evitar la inyección SQL, agrupar operaciones en transacciones y aislar el acceso en una capa propia.

## Ejercicio 9.6

**Pool de conexiones.** Programa el acceso con un **pool** (varias conexiones reutilizables en lugar de abrir una por operación). Inserta el usuario *Reina Soto* (`reina@mail.com`) y muestra los pedidos de *Lola Marín*.

!!! note "En Java"
    Usa **HikariCP**: `HikariConfig`, `HikariDataSource`; pide la conexión con `dataSource.connection` y ciérrala (la devuelves al pool) con *try-with-resources*.

## Ejercicio 9.7

**Inyección SQL.** Escribe una consulta que busque un usuario por nombre construyendo el SQL **concatenando** el texto del usuario. Pruébala con el nombre `x' OR '1'='1` y observa qué devuelve. Después reescríbela con sentencia **parametrizada** y comprueba que la entrada maliciosa ya no funciona. Explica la diferencia.

## Ejercicio 9.8

**Transacción.** Escribe `crearPedido(idUsuario, lineas)` que inserte el pedido, sus líneas y descuente el stock **como una sola operación**: si algo falla (por ejemplo, no hay stock suficiente), deshaz todo (*rollback*) y no dejes datos a medias. Demuéstralo con un pedido correcto y con uno que falle.

!!! note "En Java"
    `conn.setAutoCommit(false)`, `commit()` y `rollback()` en el `catch`.

## Ejercicio 9.9

**Patrón DAO.** Crea una clase de datos `Usuario` y una capa `UsuarioDao` con `crear`, `buscarPorId`, `listar`, `actualizar` y `eliminar`. El resto del programa no debe contener SQL: solo habla con el DAO y con objetos `Usuario`, nunca con conexiones. Añade un pequeño servicio que use el DAO (por ejemplo, registrar un usuario comprobando antes que su email no exista).
