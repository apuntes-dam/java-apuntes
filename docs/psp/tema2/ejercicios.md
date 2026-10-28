# Ejercicios del Tema 2 · Multihilos

<div class="ej-gate" data-unit="psp-t2" data-nombre="PSP · Tema 2 · Multihilos"></div>

Los cinco primeros son los **ejercicios de clase** de la sesión 1; el 6 y el 7 son de **ampliación**. Escríbelos tú, ejecútalos y compáralos con la solución. Recuerda que, cuando intervienen varios hilos, **el orden de las líneas cambia** de una ejecución a otra.

## Ejercicio 1

**¿Proceso nuevo o hilo? ¿CPU o IO?** Para cada situación, decide si usarías un **hilo** o un **proceso nuevo**, y si el trabajo es **CPU-bound** o **IO-bound**:

1. Un editor revisa la ortografía mientras escribes.
2. Chrome aísla cada pestaña para que, si una falla, no caigan las demás.
3. Un servidor web atiende a 200 clientes que consultan el mismo catálogo en memoria.
4. IntelliJ compila tu proyecto lanzando Gradle.
5. Un videojuego calcula la física de 500 objetos en cada fotograma.

<details class="sol" data-key="java/psp-t2/E1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>// Compartir datos pide hilo; aislar pide proceso.
//
// #  Usaría   Tipo  Por qué
// 1  Hilo     CPU   Necesita leer el mismo documento que estás editando.
// 2  Proceso  —     Lo importante es el aislamiento: un fallo no debe tumbar al resto.
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 2

**La cocina con tres cocineros.**

1. Crea una clase `Cocina` con un `main` que lance **3 hilos** llamados `Cocinero-1`, `Cocinero-2` y `Cocinero-3`.
2. Cada cocinero prepara 5 platos: imprime «`Cocinero-2 prepara el plato 3`» y espera 100 ms.
3. El `main` imprime «`Cocina cerrada`» **solo cuando los tres hayan terminado**.
4. *Ampliación*: que haya tantos cocineros como **núcleos** tenga tu ordenador.

Pista: `new Thread(() -> { … }, "Cocinero-" + (i + 1));`

<details class="sol" data-key="java/psp-t2/E2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Cocina {
    public static void main(String[] args) throws InterruptedException {
        Thread[] cocineros = new Thread[3];               // ampliación: Runtime.getRuntime().availableProcessors() en vez de 3
        for (int i = 0; i &lt; cocineros.length; i++) {
            cocineros[i] = new Thread(() -&gt; {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 3

**Predice los estados antes de ejecutar.**

```java
Thread lento = new Thread(() -> {
    try { Thread.sleep(2000); } catch (InterruptedException e) { }
});
Thread espera = new Thread(() -> {
    try { lento.join(); } catch (InterruptedException e) { }
});
System.out.println("A: " + espera.getState());
lento.start();
espera.start();
Thread.sleep(500);
System.out.println("B: " + lento.getState());
System.out.println("C: " + espera.getState());
espera.join();
System.out.println("D: " + espera.getState());
```

1. Apunta en papel qué imprimirá **A, B, C y D**.
2. Ejecútalo y compara.
3. Cambia `lento.join()` por `lento.join(5000)`. ¿Qué cambia?

<details class="sol" data-key="java/psp-t2/E3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Estados {
    public static void main(String[] args) throws InterruptedException {
        Thread lento = new Thread(() -&gt; {
            try { Thread.sleep(2000); } catch (InterruptedException e) { }
        });
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 4

**Un cronómetro que se para con Enter.**

1. Crea un hilo `Crono` que guarde la hora de inicio y repita un bucle con `sleep(100)`.
2. El `main` muestra «`Pulsa Enter para parar`», arranca el hilo y espera una línea con `Scanner`.
3. Al pulsar Enter, `main` **interrumpe** al cronómetro y espera a que termine.
4. El cronómetro, al pararse, imprime «`Tiempo: 3.4 s`».

Pista: el tiempo se mide con `System.currentTimeMillis()`.

<details class="sol" data-key="java/psp-t2/E4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Scanner;
public class Cronometro {
    public static void main(String[] args) throws InterruptedException {
        Thread crono = new Thread(() -&gt; {
            long ini = System.currentTimeMillis();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 5

**100 hilos de plataforma contra hilos virtuales.** Con esta función de medida:

```java
static long medir(ExecutorService exec) {
    long ini = System.currentTimeMillis();
    try (exec) {
        for (int i = 0; i < 1_000; i++) {
            exec.submit(() -> { Thread.sleep(1000); return null; });
        }
    }
    return System.currentTimeMillis() - ini;
}
```

1. Predice cuánto tardará `medir(Executors.newFixedThreadPool(100))` y cuánto `medir(Executors.newVirtualThreadPerTaskExecutor())`.
2. Ejecuta e imprime los dos tiempos.
3. Cambia el `sleep` por un bucle que **calcule durante 1 s**. ¿Siguen ganando los virtuales?

<details class="sol" data-key="java/psp-t2/E5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
public class Medir {
    static long medir(ExecutorService exec) {
        long ini = System.currentTimeMillis();
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 6 · ampliación

**Interrumpir solo a uno.** Lanza tres «obreros» que hacen cinco vueltas de 200 ms y luego imprimen «`Obrero-N termina su trabajo`». A los 500 ms, **interrumpe solo al segundo**: debe imprimir «`Obrero-2 cancelado`» y los otros dos terminar con normalidad. El `main` imprime «`Fin`» cuando los tres hayan acabado.

<details class="sol" data-key="java/psp-t2/E6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Obreros {
    public static void main(String[] args) throws InterruptedException {
        Thread[] obreros = new Thread[3];
        for (int i = 0; i &lt; obreros.length; i++) {
            obreros[i] = new Thread(() -&gt; {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 7 · ampliación

**Repartir una suma entre los núcleos.** Calcula la suma de 1 a 100.000.000 con **tantos hilos como núcleos**. Cada hilo suma **su trozo** y lo guarda en **su propio hueco** de un array (así no comparten nada que se pise). Al final, `main` suma los trozos y comprueba el resultado con la fórmula `n(n+1)/2`.

<details class="sol" data-key="java/psp-t2/E7">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class SumaRepartida {
    public static void main(String[] args) throws InterruptedException {
        int hilos = Runtime.getRuntime().availableProcessors();
        long n = 100_000_000L;
        long[] parciales = new long[hilos];                   // cada hilo escribe SOLO en su hueco: no hay carrera
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
