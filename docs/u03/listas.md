# U3.1 · Listas y tuplas

<div class="ej-gate" data-unit="u03" data-nombre="U3 · Estructuras de datos"></div>

Listas (y arrays) de datos. Si tu lenguaje no tiene tuplas, usa una clase pequeña, un *record* o un par.

## Ejercicio 3.1.1

Guarda las asignaturas de un curso (Matemáticas, Física, Química, Historia y Lengua) en una lista y muéstrala.

## Ejercicio 3.1.2

Con la lista de asignaturas, muestra para cada una el mensaje `Yo estudio <asignatura>`.

## Ejercicio 3.1.3

Con la lista de asignaturas, pregunta la nota de cada una y muestra al final `En <asignatura> has sacado <nota>` para todas.

## Ejercicio 3.1.4

Pregunta los números ganadores de la lotería primitiva, guárdalos en una lista y muéstralos ordenados de menor a mayor.

## Ejercicio 3.1.5

Guarda los números del 1 al 10 en una lista y muéstralos en orden inverso, separados por comas.

## Ejercicio 3.1.6

Con la lista de asignaturas, pregunta la nota de cada una y **elimina** de la lista las aprobadas. Al final muestra las asignaturas que hay que repetir.

## Ejercicio 3.1.7

Guarda el abecedario en una lista, elimina las letras que ocupen posiciones múltiplos de 3 y muestra la lista resultante.

## Ejercicio 3.1.8

Pide una palabra y muestra si es un **palíndromo**.

<details class="sol" data-key="u345/u3-1/3.1.8">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Palabra: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 3.1.9

Pide una palabra y muestra cuántas veces aparece cada vocal.

## Ejercicio 3.1.10

Guarda los precios 50, 75, 46, 22, 80, 65 y 8 en una lista y muestra el menor y el mayor.

<details class="sol" data-key="u345/u3-1/3.1.10">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.List;
public class Solucion {
    public static void main(String[] args) {
        List&lt;Integer&gt; precios = List.of(50, 75, 46, 22, 80, 65, 8);
        int menor = precios.get(0);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 3.1.11

Guarda los vectores (1, 2, 3) y (-1, 0, 2) en dos listas y muestra su **producto escalar**.

## Ejercicio 3.1.12

Guarda en listas anidadas las matrices

```text
A (2x3) = [1 2 3]      B (3x2) = [-1 0]
          [4 5 6]                [ 0 1]
                                 [ 1 1]
```

y muestra su producto (una matriz 2x2). Después prueba con `B · A` (resultado 3x3).

## Ejercicio 3.1.13

Pide una muestra de números separados por comas, guárdalos en una lista y muestra su **media** y su **desviación típica poblacional** (la raíz de la media de los cuadrados de las diferencias con la media, dividiendo entre `n`).
