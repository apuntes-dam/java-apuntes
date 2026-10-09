# P2.3 · Captura de excepciones

<div class="ej-gate" data-unit="u02" data-nombre="U2 · Estructuras de control"></div>

Salvo que se diga lo contrario, el objetivo es **que ninguna excepción llegue al programa principal y lo aborte**. Controla cualquier dato incorrecto que pueda introducir el usuario.

## Ejercicio 2.3.1

Pide la edad y muestra todos los años cumplidos (de 1 hasta su edad). Controla que lo escrito sea un número.

## Ejercicio 2.3.2

Pide un entero positivo y muestra los impares de 1 hasta ese número, separados por comas. Controla las entradas incorrectas.

## Ejercicio 2.3.3

Pide un entero positivo y muestra la cuenta atrás hasta cero. Repite la petición hasta que el número sea correcto.

!!! note "En Java"
    Si usas `teclado.nextInt()` y escriben letras, el texto **no se consume** y el siguiente `nextInt()` vuelve a fallar con el mismo texto (bucle infinito). Lee la línea entera con `nextLine()` y conviértela con `Integer.parseInt`.

## Ejercicio 2.3.4

Pide un entero. Si la entrada no es correcta, muestra `La entrada no es correcta` y vuelve a lanzar la excepción capturada.

<details class="sol" data-key="p2-3/2.3.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Escribe un entero: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2.3.5

Pide una contraseña y, si no coincide con la guardada, lanza una excepción propia con el mensaje `Incorrect Password!!`.

<details class="sol" data-key="p2-3/2.3.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    static class ContrasenaIncorrecta extends Exception {
        ContrasenaIncorrecta(String mensaje) {
            super(mensaje);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
