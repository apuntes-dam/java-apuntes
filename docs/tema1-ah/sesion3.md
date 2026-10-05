# Sesión 3 · Asincronía y primera app

Tras ver `Future`, `async`/`await` y crear la primera app.

## R17

**La cafetera.** Método `prepararCafe()` que devuelve un `CompletableFuture<String>` y tarda 3 s (usa `Thread.sleep` dentro de `supplyAsync`). En `main`: muestra `Enciendo la cafetera`, espera el café con `join()` y muestra `¡Café listo!`.

<details class="sol" data-key="sesion3/R17">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.CompletableFuture;
public class Solucion {
    static CompletableFuture&lt;String&gt; prepararCafe() {
        return CompletableFuture.supplyAsync(() -&gt; {
            try {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## R19

**Tres descargas.** Tres tareas `CompletableFuture` que tardan 1, 2 y 3 s. Mide el tiempo pidiéndolas una detrás de otra y después todas a la vez con `CompletableFuture.allOf`.

<details class="sol" data-key="sesion3/R19">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.concurrent.CompletableFuture;
public class Solucion {
    static CompletableFuture&lt;String&gt; descargar(String nombre, int segundos) {
        return CompletableFuture.supplyAsync(() -&gt; {
            try {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## R20

**División peligrosa.** Método `dividir(a, b)` que devuelve un `CompletableFuture<Double>`, tarda 1 s y falla si `b` es 0. Captura el error con `exceptionally` o con `try`/`catch` sobre `join()`.
