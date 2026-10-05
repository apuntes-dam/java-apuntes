# Sesión 2 · Control de flujo, funciones y clases

Tras ver control de flujo, funciones, colecciones, clases y excepciones.

## R9

**Par, impar y signo.** Recorre del -3 al 10 y muestra, por ejemplo, `-3 es impar y negativo` o `0 es par y cero`. Calcula el signo con una selección múltiple que devuelva un valor.

## R10

**FizzBuzz gaditano.** Del 1 al 30: múltiplos de 3 → `Chirigota`; de 5 → `Comparsa`; de ambos → `¡Carnaval!`; el resto, el número.

<details class="sol" data-key="sesion2/R10">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Solucion {
    public static void main(String[] args) {
        for (int n = 1; n &lt;= 30; n++) {
            if (n % 15 == 0) {
                System.out.println("¡Carnaval!");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## R11

**Tabla de multiplicar.** Escribe una función que reciba `n` y un límite opcional (por defecto 10) y devuelva la tabla en un texto de varias líneas. Llámala con y sin límite.

## R12

**¿Bisiesto?** Función `esBisiesto(anio)` que devuelva un booleano en una sola expresión. Pruébala con 2024, 2023, 1900 y 2000.

## R13

**Estadísticas de notas.** Con `List.of(6.5, 4.2, 9.1, 7.0, 3.8, 5.0)` calcula con *streams*: la media, la nota máxima, cuántas están aprobadas y la lista ordenada de mayor a menor.

## R14

**Contador de palabras.** Dado un texto, construye un mapa palabra → veces que aparece (en minúsculas y sin comas ni puntos).

## R15

**Cuenta bancaria.** Clase `CuentaBancaria` con `titular`, un saldo privado, un getter `saldo` y los métodos `ingresar` y `retirar`. Retirar más de lo que hay lanza una excepción propia que capturas en `main`.

## R16

**Figuras.** Clase abstracta `Figura` con un getter `area`; subclases `Circulo` y `Rectangulo`. Guarda varias en una lista y calcula el área total.
