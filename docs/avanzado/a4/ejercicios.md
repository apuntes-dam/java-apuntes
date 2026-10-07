# A4 · Ejercicios de pruebas automáticas

<div class="ej-gate" data-unit="a4" data-nombre="A4 · Pruebas automáticas"></div>

En cada ejercicio escribes una función **y sus pruebas**, en el mismo archivo, con el marco de pruebas de tu lenguaje. Tu archivo debe **ejecutarse y pasar todas las pruebas**: el resultado esperado indica cuántas.

## Ejercicio A4.1

**Factorial con pruebas.** Escribe `factorial(n)` (el producto de los números de `1` a `n`; el factorial de `0` es `1`) y estas **4 pruebas**:

1. El factorial de `0` es `1`.
2. El factorial de `1` es `1`.
3. El factorial de `5` es `120`.
4. Un número **negativo** lanza un error.

**Resultado esperado al ejecutar las pruebas:**

```text
4 pruebas correctas
```

!!! note "En Java"
    Con JUnit 4: métodos `@Test` en una clase pública. Para el error: `assertThrows(IllegalArgumentException.class, () -> ...)`.

<details class="sol" data-key="av/a4/A4.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertThrows;
import org.junit.Test;
class Funciones {
    static long factorial(int n) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A4.2

**Palíndromos con pruebas.** Escribe `esPalindromo(texto)`, que dice si un texto se lee igual del derecho que del revés, **sin tener en cuenta mayúsculas ni espacios**, y estas **4 pruebas**:

1. `Anita lava la tina` es palíndromo.
2. `hola` no lo es.
3. Un texto **vacío** es palíndromo.
4. `Ana` es palíndromo aunque tenga mayúsculas.

**Resultado esperado al ejecutar las pruebas:**

```text
4 pruebas correctas
```

<details class="sol" data-key="av/a4/A4.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;
import org.junit.Test;
class Funciones {
    static boolean esPalindromo(String texto) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A4.3

**Descuento con validación.** Escribe `descuento(precio, porcentaje)`, que devuelve el precio **después de rebajar** el porcentaje indicado. Los precios van en **céntimos** (enteros) y el resultado también (usa la división entera). Si el porcentaje no está entre `0` y `100`, lanza un error.

Escribe estas **4 pruebas**:

1. Con `0 %`, el precio no cambia.
2. Con `100 %`, el precio es `0`.
3. `25 %` de `2000` es `1500`.
4. Un porcentaje de `101` lanza un error.

**Resultado esperado al ejecutar las pruebas:**

```text
4 pruebas correctas
```

<details class="sol" data-key="av/a4/A4.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertThrows;
import org.junit.Test;
class Funciones {
    static int descuento(int precio, int porcentaje) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A4.4

**Un contador, probado por separado.** Escribe una clase `Contador` con una operación `incrementar` (suma 1), una operación `reiniciar` (vuelve a 0) y un valor actual. Usa la **preparación previa** de tu marco de pruebas para crear un contador **nuevo antes de cada prueba**.

Escribe estas **3 pruebas**:

1. Un contador nuevo vale `0`.
2. Tras incrementar dos veces vale `2`.
3. Tras incrementar y reiniciar vale `0`.

**Resultado esperado al ejecutar las pruebas:**

```text
3 pruebas correctas
```

!!! note "En Java"
    Un método con `@Before` se ejecuta antes de cada `@Test`.

<details class="sol" data-key="av/a4/A4.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import static org.junit.Assert.assertEquals;
import org.junit.Before;
import org.junit.Test;
class Contador {
    private int valor = 0;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A4.5

**Casos límite.** Escribe `categoria(edad)`: `niño` de `0` a `12`, `adolescente` de `13` a `17`, `adulto` de `18` a `64` y `mayor` desde `65`. Una edad **negativa** lanza un error.

Escribe **solo 2 pruebas**: una que compruebe **todos los límites** (`0`, `12`, `13`, `17`, `18`, `64` y `65`) recorriendo una tabla de casos, y otra para la edad negativa.

**Resultado esperado al ejecutar las pruebas:**

```text
2 pruebas correctas
```

!!! note "En Java"
    Un `Map` o dos arrays paralelos y un bucle dentro de **un** método `@Test`.

<details class="sol" data-key="av/a4/A4.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertThrows;
import java.util.Map;
import java.util.TreeMap;
import org.junit.Test;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A4.6

**Un reloj falso.** Escribe `saludo(reloj)`, donde `reloj` es una **función que devuelve la hora actual** (de `0` a `23`). Devuelve `buenos días` antes de las 12, `buenas tardes` antes de las 20 y `buenas noches` el resto.

Escribe estas **3 pruebas**, usando un reloj **falso** que devuelva siempre la hora que quieras: a las `8`, a las `15` y a las `22`.

**Resultado esperado al ejecutar las pruebas:**

```text
3 pruebas correctas
```

!!! note "En Java"
    Usa `java.util.function.IntSupplier`; el reloj falso es `() -> 8`.

<details class="sol" data-key="av/a4/A4.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import static org.junit.Assert.assertEquals;
import java.util.function.IntSupplier;
import org.junit.Test;
class Funciones {
    static String saludo(IntSupplier reloj) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
