# P1.2 · Primeros programas

<div class="ej-gate" data-unit="u01" data-nombre="U1 · Primer programa"></div>

Traduce algoritmos sencillos a código. Para cada ejercicio anota **entradas, proceso y salidas** antes de programar. Prueba con valores normales, cero y negativos cuando tenga sentido.

## Ejercicio 1.2.1

Pide el nombre del usuario y salúdalo.

```text
Escribe tu nombre: Juan
Hola, Juan.
```

<details class="sol" data-key="p1-2/1.2.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Escribe tu nombre: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 1.2.2

Pide las horas trabajadas y el precio por hora y muestra el importe total del servicio.

```text
Horas de trabajo: 6
Coste por hora: 10
Importe total: 60
```

## Ejercicio 1.2.3

Suponiendo estas declaraciones:

```java
int ancho = 17;
double alto = 12.0;
```

Antes de ejecutarlas, adivina el **valor** y el **tipo** de cada expresión y luego compruébalo asignando el resultado a una variable del tipo que creas:

1. `ancho / 2`
2. `ancho / 2.0`
3. `alto / 3`
4. `1 + 2 * 5`

??? success "Respuestas"
    1. `8` → `int` (`int / int` es división entera)
    2. `8.5` → `double`
    3. `4.0` → `double`
    4. `11` → `int` (primero la multiplicación)

## Ejercicio 1.2.4

Pide una temperatura en grados Celsius, conviértela a Fahrenheit y muéstrala.

<details class="sol" data-key="p1-2/1.2.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Grados Celsius: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 1.2.5

Pide el importe de un artículo sin IVA y el tipo de IVA (por ejemplo, 21) y muestra el precio final.

## Ejercicio 1.2.6

Pide el precio final de un artículo y, suponiendo un IVA del 10 %, muestra el IVA pagado y el importe sin IVA.

## Ejercicio 1.2.7

Pide tres números y muestra su suma.

!!! note "En Java"
    Si el usuario escribe los tres números en una sola línea (`4 5 6`), puedes leerla entera y separarla con `teclado.nextLine().split(" ")`.

## Ejercicio 1.2.8

Pide una cantidad de segundos y muéstrala en horas, minutos y segundos. Por ejemplo, `3725` segundos son `1 h 2 min 5 s`.

## Ejercicio 1.2.9

Guarda dos valores en las variables `a` y `b`, pedidos por teclado. Intercambia sus valores (que `a` acabe con lo que tenía `b` y al revés) y muestra ambas variables antes y después del intercambio.

## Ejercicio 1.2.10

Muestra por pantalla el resultado de esta operación: `3 + 5 · (2 + 4)² ÷ 6`. Respeta la precedencia de los operadores y usa la potencia de tu lenguaje.

*(El enunciado original es una imagen; aquí se sustituye por una operación equivalente.)*

## Ejercicio 1.2.11

Lee un entero positivo `n` y muestra la suma de los enteros de 1 a `n`. Hazlo de dos formas: con un bucle y con la fórmula `n · (n + 1) / 2`.

## Ejercicio 1.2.12

Pide peso (kg) y estatura (m), calcula el índice de masa corporal (peso / estatura²) y muestra `Tu índice de masa corporal es X`, con X redondeado a 2 decimales.

!!! note "En Java"
    `printf("%.2f")` y `String.format` usan el idioma del equipo: en uno en español escriben `22,86` con coma. Si quieres siempre el punto, pasa `Locale.US` como primer argumento.

<details class="sol" data-key="p1-2/1.2.12">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Peso (kg): ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 1.2.13

Pide dos enteros `n` y `m` y muestra `la división de n entre m da un cociente c y un resto r`. Controla también la división entre cero.

<details class="sol" data-key="p1-2/1.2.13">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("n: ");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 1.2.14

Una juguetería envía payasos (112 g cada uno) y muñecas (75 g cada una). Lee cuántos de cada se han vendido y calcula el peso total del paquete.

## Ejercicio 1.2.15

Una cuenta de ahorros da un 4 % de interés anual que se suma al saldo a final de año. Lee el dinero depositado y muestra el saldo tras el primer, segundo y tercer año, redondeado a 2 decimales. Fórmula de cada año: `capital · (1 + interés)`.

!!! note "En Java"
    `printf("%.2f")` y `String.format` usan el idioma del equipo: en uno en español escriben `22,86` con coma. Si quieres siempre el punto, pasa `Locale.US` como primer argumento.

## Ejercicio 1.2.16

Una panadería vende barras a 3,49 € (defínelo como constante). Las que no son del día tienen un 60 % de descuento. Lee cuántas barras no frescas se venden y muestra el precio habitual, el descuento aplicado y el coste total.

## Ejercicio 1.2.17

Pide un nombre y un entero `n` y muestra el nombre `n` veces, cada una en una línea.

## Ejercicio 1.2.18

Pide el nombre completo y muéstralo tres veces: todo en minúsculas, todo en mayúsculas y con la inicial de cada palabra en mayúscula. El usuario puede escribirlo con cualquier combinación de mayúsculas y minúsculas.

!!! note "En Java"
    Java no trae una función para esto: separa las palabras con `split(" ")` y, en cada una, junta `substring(0, 1).toUpperCase()` con el resto en minúsculas.

## Ejercicio 1.2.19

Pide un nombre y muestra `NOMBRE tiene n letras.`, con el nombre en mayúsculas.

## Ejercicio 1.2.20

Los teléfonos de una empresa tienen el formato `prefijo-número-extensión`, por ejemplo `+34-913724710-56`. Pide uno y muestra solo el número, sin prefijo ni extensión.

## Ejercicio 1.2.21

Pide una frase y muéstrala invertida.

## Ejercicio 1.2.22

Pide una frase y una vocal, y muestra la frase con esa vocal en mayúscula.

## Ejercicio 1.2.23

Pide un correo electrónico y muestra otro con el mismo nombre de usuario (lo que va antes de la `@`) pero con el dominio `ceu.es`.

## Ejercicio 1.2.24

Pide el precio de un producto en euros con dos decimales y muestra cuántos euros y cuántos céntimos son.

!!! note "En Java"
    No obtengas los céntimos multiplicando el decimal por 100: `19.99 * 100` no da exactamente `1999` sino `1998.9999999999998`, y al quedarte con la parte entera salen 1998. Separa el texto por el punto o redondea.

## Ejercicio 1.2.25

Pide una fecha de nacimiento con formato `dd/mm/aaaa` y muestra día, mes y año. Después adáptalo para que funcione si el día o el mes se escriben con un solo dígito.

## Ejercicio 1.2.26

Pide los productos de una cesta de la compra separados por comas y muestra cada producto en una línea.

## Ejercicio 1.2.27

Pide el nombre de un producto, su precio y las unidades, y muestra una línea con el nombre, el precio unitario (6 dígitos enteros y 2 decimales), las unidades (3 dígitos) y el coste total (8 dígitos enteros y 2 decimales).

!!! note "En Java"
    Con `printf`, `%9.2f` reserva 9 posiciones con 2 decimales (y en un equipo en español pone coma; ver la nota del ejercicio 1.2.12).

## Ejercicio 1.2.28

Calcula el área de un triángulo a partir de sus tres lados (fórmula de Herón). Indica qué ocurre si las longitudes no pueden formar un triángulo.

!!! note "En Java"
    `Math.sqrt` de un número negativo no lanza ninguna excepción: devuelve `NaN`. Compruébalo antes de calcular.

## Ejercicio 1.2.29

Genera un número aleatorio entre dos valores que introduce el usuario (ambos incluidos).

## Ejercicio 1.2.30

Determina si un número es primo. Ten en cuenta el 0, el 1 y los negativos.

## Ejercicio 1.2.31

Muestra todos los divisores de un número.

## Ejercicio 1.2.32

Muestra la serie de Fibonacci hasta un número dado.

## Herramientas útiles en Java

| Necesitas... | Usa... |
|---|---|
| Leer una línea | `Scanner teclado = new Scanner(System.in); teclado.nextLine()` |
| Texto a número | `Integer.parseInt(...)`, `Double.parseDouble(...)` |
| Redondear al mostrar | `System.out.printf("%.2f%n", x)` o `String.format("%.2f", x)` |
| Potencia y raíz | `Math.pow(x, 2)`, `Math.sqrt(x)` |
| Aleatorios | `new Random().nextInt(n)` (de `java.util`) o `Math.random()` |
| Mayúsculas / minúsculas | `toUpperCase()`, `toLowerCase()` |
| Cortar y unir texto | `split(",")`, `substring(a, b)`, `String.join(", ", lista)` |
| Comparar textos | `a.equals(b)` y `a.equalsIgnoreCase(b)` |
| Lanzar una excepción | `throw new ...` |

!!! note "Decimales con coma"
    `printf` usa la configuración regional del sistema: en un equipo en español mostrará `22,86`. Para forzar el punto, usa `String.format(Locale.US, "%.2f", x)`.

Para ejecutar cada ejercicio: `java Ejercicio.java`.