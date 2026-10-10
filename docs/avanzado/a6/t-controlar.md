# A6.C Controlar y observar procesos

## Esperar con límite y detener

`waitFor()` sin más **espera para siempre**. Lo prudente es poner un **límite de tiempo** y, si se pasa, **detener** al hijo en dos pasos: primero **pedirle** que acabe y, si no lo hace, **obligarle**.

| Método | Qué hace |
|---|---|
| `waitFor(tiempo, unidad)` | Espera como mucho ese tiempo. Devuelve `true` si el hijo ha terminado y `false` si no |
| `isAlive()` | ¿Sigue en marcha? |
| `destroy()` | Pide que termine. En Linux envía una señal que el proceso puede atender para **cerrar con orden** |
| `destroyForcibly()` | Lo mata sin contemplaciones |
| `exitValue()` | Código de salida. **Lanza una excepción si el hijo aún no ha terminado** |

```java
import java.io.IOException;
import java.nio.file.Path;
import java.util.concurrent.TimeUnit;

public class Controlar {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        Process p = new ProcessBuilder(java, "Dormilon.java").start();
        System.out.println("¿Sigue vivo? " + p.isAlive());

        if (!p.waitFor(2, TimeUnit.SECONDS)) {          // espera como mucho 2 segundos
            System.out.println("No ha terminado a tiempo: se le pide que acabe");
            p.destroy();                                // petición de cierre ordenado
            if (!p.waitFor(2, TimeUnit.SECONDS)) {
                p.destroyForcibly();                    // se acabó la paciencia
            }
        }
        System.out.println("¿Sigue vivo? " + p.isAlive());
        System.out.println("Código de salida: " + p.exitValue());
    }
}
```

**Salida:**

```text
¿Sigue vivo? true
No ha terminado a tiempo: se le pide que acabe
¿Sigue vivo? false
Código de salida: 1
```

!!! info "En Windows no hay diferencia entre `destroy()` y `destroyForcibly()`"
    La diferencia entre pedir y obligar es de **Linux y macOS** (`destroy()` envía la señal de terminar y `destroyForcibly()` la de matar). En **Windows** los dos terminan el proceso a la fuerza, y por eso aquí `destroy()` basta. Conviene escribir los dos pasos igualmente para que el programa funcione en cualquier sistema.

El código de salida de un proceso detenido **depende del sistema operativo**: en Windows ha salido 1; en Linux suele ser 143 (128 más la señal 15 de `destroy()`), un dato que no se ha ejecutado aquí.

## Avisar al terminar: `onExit()`

En vez de bloquearse esperando, el padre puede seguir trabajando y pedir que se le **avise** cuando el hijo termine. `onExit()` devuelve un `CompletableFuture`:

```java
import java.io.IOException;
import java.nio.file.Path;
import java.util.concurrent.CompletableFuture;

public class AlTerminar {
    public static void main(String[] args) throws IOException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        Process p = new ProcessBuilder(java, "Sumador.java", "1", "2").start();
        CompletableFuture<Void> aviso = p.onExit()
                .thenAccept(proceso -> System.out.println("El hijo ha terminado con código " + proceso.exitValue()));
        System.out.println("El padre sigue trabajando mientras tanto...");
        aviso.join();                                   // solo para que el ejemplo no acabe antes
    }
}
```

**Salida:**

```text
El padre sigue trabajando mientras tanto...
El hijo ha terminado con código 0
```

## `ProcessHandle`: el administrador de tareas

`ProcessHandle` (desde Java 9) permite **observar y controlar cualquier proceso**, no solo los que has lanzado tú: el actual (`ProcessHandle.current()`), su padre, sus hijos, y consultar su información (`info()`: programa, usuario, hora de inicio, CPU...). `hijo.toHandle()` convierte un `Process` en un `ProcessHandle`.

```java
import java.io.IOException;
import java.nio.file.Path;

public class Observar {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        ProcessHandle yo = ProcessHandle.current();
        System.out.println("Mi PID: " + yo.pid());
        System.out.println("PID de mi padre: " + yo.parent().map(ProcessHandle::pid).orElse(-1L));

        Process hijo = new ProcessBuilder(java, "Dormilon.java").start();
        ProcessHandle h = hijo.toHandle();
        System.out.println("Mis hijos directos: " + yo.children().count());
        System.out.println("Programa del hijo: " + h.info().command().map(c -> Path.of(c).getFileName().toString()).orElse("?"));
        System.out.println("¿Vivo? " + h.isAlive());

        h.destroy();                                    // se detiene el proceso a través de su manejador
        h.onExit().join();                              // y se espera a que de verdad haya terminado
        System.out.println("¿Vivo? " + h.isAlive());
    }
}
```

**Salida:**

```text
Mi PID: 25716
PID de mi padre: 12064
Mis hijos directos: 1
Programa del hijo: java.exe
¿Vivo? true
¿Vivo? false
```

`allProcesses()` devuelve **todos** los procesos que puedes ver, y sobre cada uno se puede llamar a `info()`, `isAlive()` o `destroy()`.

## Encadenar procesos

`ProcessBuilder.startPipeline(...)` conecta la salida de un proceso con la entrada del siguiente, como la tubería `|` de la shell, pero sin pasar por ella:

```java
// SIN EJECUTAR aquí: usa programas de Linux (ps, grep, wc). Equivale a:  ps -e | grep java | wc -l
List<Process> tuberia = ProcessBuilder.startPipeline(List.of(
        new ProcessBuilder("ps", "-e"),
        new ProcessBuilder("grep", "java"),
        new ProcessBuilder("wc", "-l")));
String cuantos = new String(tuberia.get(tuberia.size() - 1).getInputStream().readAllBytes()).trim();
```

## Qué recordar

* Pon **siempre un límite** a la espera (`waitFor(t, unidad)`) y detén en dos pasos: `destroy()` y, si hace falta, `destroyForcibly()`.
* `exitValue()` solo se puede llamar cuando el proceso **ha terminado**.
* El código de salida de un proceso detenido **cambia según el sistema**.
* `onExit()` avisa sin bloquear; `ProcessHandle` observa y controla cualquier proceso.

Ahora, a practicar: [ejercicios A6.1 a A6.6](ejercicios.md).
