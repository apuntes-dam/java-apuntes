# A5 · Ejercicios de patrones de diseño

<div class="ej-gate" data-unit="a5" data-nombre="A5 · Patrones de diseño"></div>

Practica fábrica, singleton, builder, estrategia, observador y decorador. Cada ejercicio indica la **salida esperada**.

## Ejercicio A5.1

**Fábrica de figuras.** Crea un tipo común `Figura` con un valor `area` y tres figuras: `Cuadrado` (lado `3`), `Rectangulo` (`2` por `5`) y `Triangulo` (base `4`, altura `3`; el área es base por altura entre `2`, con división entera).

Escribe `crearFigura(nombre)`, que devuelve la figura que corresponde a `cuadrado`, `rectangulo` o `triangulo`, y para cualquier otro nombre lanza un error con el mensaje `figura desconocida: <nombre>`. Pruébala con `cuadrado`, `rectangulo`, `triangulo` y `hexagono`: muestra `nombre: area` y, en el último caso, el mensaje del error.

**Salida esperada:**

```text
cuadrado: 9
rectangulo: 10
triangulo: 6
figura desconocida: hexagono
```

!!! note "En Java"
    Una interfaz `Figura` y un `switch` con flechas; el error con `IllegalArgumentException` y su texto en `getMessage()`.

<details class="sol" data-key="av/a5/A5.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.List;
public class Solucion {
    interface Figura {
        int area();
    }
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A5.2

**Un registro único.** Crea una clase `Registro` que sea un **singleton**: solo puede existir una instancia y se obtiene siempre la misma. Debe guardar una lista de mensajes y tener una operación `log(mensaje)`.

En `main`, obtén el registro, añade el mensaje `inicio`; **vuelve a obtener el registro** en otra variable y añade `fin`. Muestra cuántos mensajes tiene el registro y si las dos variables son **la misma instancia**.

**Salida esperada:**

```text
mensajes: 2
misma instancia: sí
```

!!! note "En Java"
    Constructor privado y un método estático que devuelve una instancia creada una sola vez; `a == b` compara referencias.

<details class="sol" data-key="av/a5/A5.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.ArrayList;
import java.util.List;
public class Solucion {
    static final class Registro {
        private static final Registro UNICO = new Registro();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A5.3

**Un correo con builder.** Crea una clase `Correo` que se construya con un **builder**: `para(...)`, `asunto(...)` y `conCopia(...)` (se puede llamar varias veces, una por cada dirección en copia). Cada correo se muestra como `para: <dirección> | asunto: <asunto> | cc: <copias o ninguno>`, con las copias separadas por coma y espacio.

Construye dos correos: `ana@mail.com` con asunto `Hola` y sin copias; y `luis@mail.com` con asunto `Reunión` y copia a `ana@mail.com` y `eva@mail.com`.

**Salida esperada:**

```text
para: ana@mail.com | asunto: Hola | cc: ninguno
para: luis@mail.com | asunto: Reunión | cc: ana@mail.com, eva@mail.com
```

!!! note "En Java"
    Una clase interna estática `Builder`; cada método devuelve `this` para poder encadenar.

<details class="sol" data-key="av/a5/A5.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.ArrayList;
import java.util.List;
public class Solucion {
    static final class Correo {
        private final String para;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A5.4

**Ordenar con estrategias.** Crea una clase `Ordenador` que reciba una **estrategia** (cómo ordenar una lista de palabras) y una operación `ordenar(lista)`. Escribe tres estrategias: **alfabética**, **por longitud** (de la más corta a la más larga) e **inversa** (la lista del revés, sin ordenar).

Aplica las tres a `pera, uva, manzana, fresa` y muestra cada resultado con su nombre (`alfabetico`, `por longitud`, `inverso`), con las palabras separadas por coma y espacio.

**Salida esperada:**

```text
alfabetico: fresa, manzana, pera, uva
por longitud: uva, pera, fresa, manzana
inverso: fresa, manzana, uva, pera
```

!!! note "En Java"
    Una estrategia puede ser un `Function<List<String>, List<String>>`; `stream().sorted(...)` no modifica la original.

<details class="sol" data-key="av/a5/A5.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.function.Function;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A5.5

**Un termómetro observable.** Crea un `Termometro` al que se pueden suscribir **observadores** y que tiene una operación `fijar(grados)` que les avisa a todos. Suscribe, por este orden, una **pantalla** (muestra `pantalla: <grados>` siempre) y una **alarma** (muestra `alarma: <grados>` solo si la temperatura es mayor que `30`).

Fija la temperatura en `25` y después en `32`.

**Salida esperada:**

```text
pantalla: 25
pantalla: 32
alarma: 32
```

!!! note "En Java"
    Una lista de `IntConsumer` o `Consumer<Integer>`; avisa recorriéndola en orden.

<details class="sol" data-key="av/a5/A5.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.ArrayList;
import java.util.List;
import java.util.function.IntConsumer;
public class Solucion {
    static final class Termometro {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A5.6

**Decoradores de texto.** Crea un tipo común `Texto` con un valor `contenido`, una clase `Simple` que guarda un texto, y dos **decoradores**: `Corchetes`, que rodea el contenido con `[` y `]`, y `Repetir`, que repite el contenido dos veces separado por un guion `-`.

Con el texto `ok`, muestra el resultado de `Repetir(Corchetes(Simple))` y el de `Corchetes(Repetir(Simple))`. Observa que **el orden de los decoradores importa**.

**Salida esperada:**

```text
[ok]-[ok]
[ok-ok]
```

<details class="sol" data-key="av/a5/A5.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Solucion {
    interface Texto {
        String contenido();
    }
    record Simple(String valor) implements Texto {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
