# A3 · Ejercicios de concurrencia y asincronía

<div class="ej-gate" data-unit="a3" data-nombre="A3 · Concurrencia y asincronía"></div>

Practica tareas simultáneas, resultados y errores, límites de tiempo, reintentos y datos compartidos. Cada ejercicio indica la **salida esperada**. Las esperas son de decenas de milisegundos, así que los resultados son siempre los mismos.

## Ejercicio A3.1

**Tres descargas a la vez.** Simula tres descargas con una pausa cada una: `uno` (200 ms), `dos` (50 ms) y `tres` (100 ms). Cada descarga imprime `termina <nombre>` al acabar.

Lánzalas **todas a la vez**, espera a que terminen y comprueba que el conjunto tardó **menos de 350 ms** (si fueran una tras otra tardarían unos 350 ms o más). Imprime `todas a la vez: sí` o `todas a la vez: no`.

**Salida esperada:**

```text
termina dos
termina tres
termina uno
todas a la vez: sí
```

!!! note "En Java"
    Con `CompletableFuture.runAsync(...)` y `CompletableFuture.allOf(...).join()`, o con un `ExecutorService`.

<details class="sol" data-key="av/a3/A3.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.CompletableFuture;
public class Solucion {
    static void descarga(String nombre, int ms) {
        try {
            Thread.sleep(ms);
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A3.2

**Sumar en paralelo.** Reparte la suma de los números del `1` al `100` entre **dos tareas** que trabajan a la vez: una suma del `1` al `50` y la otra del `51` al `100`. Espera las dos y muestra la suma total.

**Salida esperada:**

```text
5050
```

!!! note "En Java"
    Un `ExecutorService` con dos hilos y `submit(Callable)`; `future.get()` devuelve el resultado.

<details class="sol" data-key="av/a3/A3.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
public class Solucion {
    static int sumar(int desde, int hasta) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A3.3

**Con límite de tiempo.** Escribe una función asíncrona `consulta(ms)` que espere `ms` milisegundos y devuelva `42`. Después escribe `intentar(ms, limiteMs)`, que espera el resultado de la consulta **como máximo** `limiteMs` milisegundos: si llega a tiempo imprime `resultado: 42`, y si no, imprime `tiempo agotado`.

Pruébala con `intentar(300, 100)` y con `intentar(50, 200)`.

**Salida esperada:**

```text
tiempo agotado
resultado: 42
```

!!! note "En Java"
    `future.get(limite, TimeUnit.MILLISECONDS)` lanza `TimeoutException` si se pasa el tiempo.

<details class="sol" data-key="av/a3/A3.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.CompletableFuture;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;
public class Solucion {
    static int consulta(int ms) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A3.4

**Reintentos.** Escribe una operación asíncrona que **falle las dos primeras veces** que se llama y funcione la tercera (devolviendo el texto `listo`). Escribe `conReintentos(maximo)`, que llama a la operación y, si falla, la repite hasta `maximo` veces en total.

En cada intento imprime `intento N falló` o `intento N correcto`; al final, `resultado: listo`. Prueba con un máximo de 3.

**Salida esperada:**

```text
intento 1 falló
intento 2 falló
intento 3 correcto
resultado: listo
```

<details class="sol" data-key="av/a3/A3.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.CompletableFuture;
import java.util.concurrent.CompletionException;
import java.util.concurrent.atomic.AtomicInteger;
public class Solucion {
    static final AtomicInteger llamadas = new AtomicInteger();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A3.5

**Un contador compartido, bien protegido.** Lanza **5 tareas** que suman 200 veces cada una a un mismo contador, y muestra el valor final (siempre `1000`).

Hazlo de forma que **no se pierda ninguna suma** aunque las tareas se ejecuten a la vez.

**Salida esperada:**

```text
1000
```

!!! note "En Java"
    Un bloque `synchronized` (o `AtomicInteger`) evita que dos hilos mezclen el «leer, sumar, escribir».

<details class="sol" data-key="av/a3/A3.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.ArrayList;
import java.util.List;
public class Solucion {
    static int contador = 0;
    static final Object CANDADO = new Object();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A3.6

**Productor y consumidor.** Un **productor** envía los números del `1` al `5` y un **consumidor** los va recibiendo y sumando. Se comunican por un **canal** (cola, *stream*...), no por una variable compartida. Cuando el productor termina, avisa de que no hay más.

Muestra solo la suma final.

**Salida esperada:**

```text
suma: 15
```

!!! note "En Java"
    Una `BlockingQueue` entre dos hilos; un valor especial (por ejemplo `-1`) puede indicar «fin».

<details class="sol" data-key="av/a3/A3.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
public class Solucion {
    static final int FIN = -1;
    public static void main(String[] args) throws Exception {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
