# Sesión 1 · Primeros pasos

Ejercicios cortos (5-10 minutos) para coger soltura con variables, operadores, cadenas y null safety. Cada uno en su propio archivo.

## R1

**Hola.** Muestra tu nombre, tu ciclo y tu ciudad en tres líneas. Después guarda esos datos en variables y muéstralos en una sola línea con interpolación: `Soy Lucía, estudio 2º DAM en Cádiz`.

## R3

**Ticket del bar.** Con las variables `producto` (texto), `precio` (decimal), `unidades` (entero) y `terraza` (booleano), calcula el total (si es en terraza, suma un 10 %) y muéstralo así:

```text
3 x Café con leche a 1.40 € = 4.62 € (terraza)
```

Redondea a 2 decimales. Con `terraza = false` no debe aparecer el paréntesis.

<details class="sol" data-key="sesion1/R3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Locale;
public class Solucion {
    public static void main(String[] args) {
        String producto = "Café con leche";
        double precio = 1.40;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## R4

**Conversor.**

1. Pasa 25 °C, 0 °C y 40 °C a Fahrenheit (`F = C · 9 / 5 + 32`).
2. Declara una constante `final double TASA_DOLAR = 1.08` y convierte 50 € a dólares.
3. Responde: ¿qué diferencia hay entre `final` y `static final`?

## R5

**Segundos a horas.** Dado `segundos = 7384`, muestra `2 h 3 min 4 s` usando la división entera y el resto. Extra: muéstralo como `02:03:04`, rellenando con ceros.

<details class="sol" data-key="sesion1/R5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Solucion {
    public static void main(String[] args) {
        int segundos = 7384;
        int horas = segundos / 3600;
        int minutos = (segundos % 3600) / 60;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## R6

**¿Tienes apodo?** Declara `String nombre = "Francisco"` y un `String apodo = null`.

1. Saluda con el apodo si lo tiene y, si no, con el nombre, en una sola línea (operador ternario).
2. Muestra la longitud del apodo sin que lance `NullPointerException`.
3. Dale el valor `"Curro"` y vuelve a ejecutar. ¿Qué cambia?

## R7

**Iniciales.** Con `nombre = 'maría'`, `apellido1 = 'ruiz'` y `apellido2 = 'pérez'`:

1. Muestra las iniciales en mayúsculas: `M.R.P.`.
2. Muestra el nombre completo con la inicial de cada parte en mayúscula: `María Ruiz Pérez`.
3. Muestra cuántas letras tiene en total, sin contar espacios.

## R8

**Reto: ¿mayor de edad?** (solo en local) Pide la edad por teclado, convirtiéndola de forma segura.

* Si no es un número: `"hola" no es una edad válida`.
* Si lo es: indica si es mayor de edad y cuántos años faltan para los 18 (o cuántos han pasado).
