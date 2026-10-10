# A6 · Ejercicios de procesos

<div class="ej-gate" data-unit="a6" data-nombre="A6 · Procesos desde Java"></div>

Todos usan los **programas hijo** de [A6.A](t-lanzar.md) (guárdalos en la carpeta de trabajo). Los códigos de salida y los PID pueden variar de un sistema a otro: lo que se pide se comprueba con tu propia ejecución.

## Ejercicio A6.1

**El cocinero y el pinche.** El padre pide dos números por teclado, lanza `Sumador` con ellos, muestra lo que responde el hijo y, según su **código de salida**, escribe `Suma correcta (no pasa de 100)` o `La suma es demasiado grande`.

<details class="sol" data-key="jp/a6/A6.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.io.IOException;
import java.nio.file.Path;
import java.util.Scanner;
public class CocineroPinche {
    public static void main(String[] args) throws IOException, InterruptedException {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A6.2

**Contar errores.** Lanza `Charlatan` mezclando la salida de error con la normal (`redirectErrorStream`). Lee todas las líneas y muestra cuántas ha escrito el hijo en total y cuántas contienen la palabra `ERROR`. Muestra también su código de salida.

<details class="sol" data-key="jp/a6/A6.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.io.BufferedReader;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Path;
public class ContarErrores {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A6.3

**Ordenar con el sistema.** Pide palabras por teclado hasta que se escriba `fin`, **envíaselas** a la orden `sort` del sistema (existe en Windows y en Linux), cierra el flujo y muestra las palabras que devuelve ya ordenadas. Cuida que no se quede esperando datos para siempre.

<details class="sol" data-key="jp/a6/A6.3">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.util.Scanner;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A6.4

**Con límite de tiempo.** Pide un número de segundos y lanza `Dormilon` (que duerme 30). Espera como máximo esos segundos: si no ha terminado, pídele que acabe con `destroy()` y, si sigue vivo dos segundos después, con `destroyForcibly()`. Muestra si terminó por sí mismo o hubo que detenerlo, y si sigue vivo al final.

<details class="sol" data-key="jp/a6/A6.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.io.IOException;
import java.nio.file.Path;
import java.util.Scanner;
import java.util.concurrent.TimeUnit;
public class ConLimite {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A6.5

**Mini administrador de tareas.** Pide cuántos `Dormilon` lanzar (de 1 a 3). Lánzalos y muestra el **PID** de cada uno y si está vivo. Detén el primero a través de su `ProcessHandle`, espera a que termine y vuelve a mostrar la lista. Al final, detén los que queden.

<details class="sol" data-key="jp/a6/A6.5">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.io.IOException;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio A6.6

**Registro en ficheros.** Lanza `Charlatan` **dos veces**. En cada una, manda la salida normal a `salida.log` (sobrescribiendo) y la de error a `errores.log` (**añadiendo**). Al final, muestra cuántas líneas tiene cada fichero y explica por qué no coinciden con lo que esperarías si los dos se sobrescribieran.

<details class="sol" data-key="jp/a6/A6.6">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>
