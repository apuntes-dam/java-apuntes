# U6.2 · Principios SOLID

<div class="ej-gate" data-unit="u06" data-nombre="U6 · Diseño de programas con POO"></div>

Cada ejercicio parte de un código con un problema de diseño. Escribe primero la versión "mala" (breve) y luego **refactorízala**. Explica en un comentario qué principio aplicas y qué has ganado.

## Ejercicio 6.6

**S · Responsabilidad única.** Una clase `Factura` calcula el total, lo **guarda en un archivo** y lo **imprime en consola** ("hace de todo"). Sepárala en clases con una sola responsabilidad (por ejemplo `Factura`, `RepositorioFacturas` y `ImpresoraFacturas`).

## Ejercicio 6.7

**O · Abierto/cerrado.** Una función `calcularDescuento(tipoCliente, importe)` usa una cadena de `if/else` por tipo de cliente. Cada vez que aparece un tipo nuevo hay que editarla. Refactorízala para que añadir un tipo nuevo **no obligue a modificar** el código existente (por ejemplo, con una interfaz `Descuento` y una clase por tipo).

## Ejercicio 6.8

**L · Sustitución de Liskov.** Define `Ave` con el método `volar()` y crea `Gorrion` y `Pinguino` (que no vuela). Observa por qué heredar `Pinguino` de `Ave` rompe el programa cuando recorres una lista de aves y llamas a `volar()`. Rediseña la jerarquía para que cualquier subclase pueda **sustituir** a su padre sin sorpresas.

## Ejercicio 6.9

**I · Segregación de interfaces.** Una interfaz `Trabajador` obliga a implementar `programar()`, `testear()` y `diseñar()`. Un `Disenador` solo diseña. Divide la interfaz en otras más pequeñas para que ninguna clase quede obligada a implementar métodos que no usa.

## Ejercicio 6.10

**D · Inversión de dependencias.** Una clase `Notificador` crea dentro un `ServicioCorreo` concreto y lo usa. Cambia el diseño para que `Notificador` dependa de una **abstracción** (`Canal`) que reciba desde fuera. Demuestra que puedes cambiar a SMS sin tocar `Notificador`, y pasa un canal falso para probarlo.
