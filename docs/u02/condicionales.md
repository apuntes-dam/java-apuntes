# P2.1 · Sentencias condicionales

<div class="ej-gate" data-unit="u02" data-nombre="U2 · Estructuras de control"></div>

Practica `if`, `else`, `else if`, operadores lógicos y selección múltiple.

## Ejercicio 2.1.1

Pide la edad y muestra si la persona es mayor de edad o no.

## Ejercicio 2.1.2

Guarda una contraseña en una variable, pide al usuario que la escriba y muestra si coincide **sin distinguir mayúsculas de minúsculas**.

## Ejercicio 2.1.3

Pide dos números y muestra su división. Si el divisor es cero, muestra un error.

## Ejercicio 2.1.4

Pide un entero y muestra si es par o impar.

<details class="sol" data-key="p2-1/2.1.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Número: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2.1.5

Para tributar un impuesto hay que ser mayor de 16 años **y** tener unos ingresos mensuales de 1000 € o más. Pregunta la edad y los ingresos y muestra si debe tributar.

## Ejercicio 2.1.6

Un curso se divide en dos grupos según sexo y nombre. El grupo A son las mujeres con nombre anterior a la M y los hombres con nombre posterior a la N; el grupo B, el resto. Pregunta nombre y sexo y muestra el grupo.

## Ejercicio 2.1.7

Los tramos de la renta anual son:

| Renta | Tipo |
|---|---|
| Menos de 10 000 € | 5 % |
| De 10 000 € a 20 000 € | 15 % |
| De 20 000 € a 35 000 € | 20 % |
| De 35 000 € a 60 000 € | 30 % |
| Más de 60 000 € | 45 % |

Pregunta la renta y muestra el tipo que corresponde.

<details class="sol" data-key="p2-1/2.1.7">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Renta anual: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2.1.8

Los empleados reciben una puntuación anual de 0.0, 0.4, 0.6 o más (no hay valores intermedios). El nivel es *Inaceptable* (0.0), *Aceptable* (0.4) o *Meritorio* (0.6 o más), y el dinero recibido es 2400 € multiplicado por la puntuación. Lee la puntuación y muestra el nivel y la cantidad.

## Ejercicio 2.1.9

Una sala de juegos cobra según la edad: gratis si es menor de 4 años, 5 € de 4 a 18 años y 10 € si es mayor de 18. Pregunta la edad y muestra el precio.

<details class="sol" data-key="p2-1/2.1.9">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Edad del cliente: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2.1.10

Una pizzería ofrece pizzas vegetarianas (pimiento, tofu) y no vegetarianas (peperoni, jamón, salmón). Pregunta qué tipo quiere el cliente, muestra los ingredientes disponibles y deja elegir **uno**. Al final muestra si la pizza es vegetariana y todos sus ingredientes (todas llevan mozzarella y tomate).
