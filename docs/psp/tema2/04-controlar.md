# 2.4 Controlar hilos

Esperar a que acabe, dormirlo, pedirle que pare y decidir si la JVM debe esperarlo al salir.

## `sleep` y `join`: dormir y esperar

| Llamada | Qué hace |
|---|---|
| **`Thread.sleep(ms)`** | **Duerme al hilo que la llama**, durante ese tiempo. No gasta CPU. Es `static`: **no duermes a otro hilo, solo a ti mismo** |
| **`t.join()`** | El hilo que llama **espera hasta que `t` termine**. Si `t` ya terminó, vuelve al instante |
| **`t.join(ms)`** | Espera **como mucho** ese tiempo y sigue. Después se comprueba con `t.isAlive()` si terminó o no |

Los tres pueden lanzar **`InterruptedException`**: es la forma en que Java avisa de que alguien ha pedido **a ese hilo** que pare.

```java
public class EsperarConLimite {
    public static void main(String[] args) throws InterruptedException {
        Thread lento = new Thread(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) { }
        });
        lento.start();
        lento.join(300);                                  // espera como mucho 300 ms
        System.out.println("Tras join(300): ¿sigue vivo? " + lento.isAlive());
        lento.join();                                     // espera hasta que termine
        System.out.println("Tras join(): ¿sigue vivo? " + lento.isAlive());
    }
}
```

**Salida:**

```text
Tras join(300): ¿sigue vivo? true
Tras join(): ¿sigue vivo? false
```

## `interrupt()`: pedir a un hilo que pare

`interrupt()` **pone una marca** en el hilo. Si está en `sleep`, `join` o `wait`, salta `InterruptedException`. Es **cooperativo**: pide parar, **no mata**; el hilo decide cómo y cuándo parar, y puede dejarlo todo ordenado.

```java
public class Reloj {
    public static void main(String[] args) throws InterruptedException {
        Thread reloj = new Thread(() -> {
            int s = 0;
            try {
                while (true) {
                    System.out.println("Tic " + s++);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Me han interrumpido: paro");
            }
        });
        reloj.start();
        Thread.sleep(3500);
        reloj.interrupt();                                // pide parar, no mata
        reloj.join();
    }
}
```

**Salida:**

```text
Tic 0
Tic 1
Tic 2
Tic 3
Me han interrumpido: paro
```

Si el hilo está **calculando** (sin `sleep` ni `join`), no hay excepción: debe **mirar la marca él mismo** con `isInterrupted()`.

```java
public class Calculador {
    public static void main(String[] args) throws InterruptedException {
        Thread calculo = new Thread(() -> {
            long suma = 0;
            while (!Thread.currentThread().isInterrupted()) {      // sin sleep ni join: hay que mirar la marca
                suma++;
            }
            System.out.println("Vi la marca de interrupción y paro");
        });
        calculo.start();
        Thread.sleep(200);
        calculo.interrupt();
        calculo.join();
    }
}
```

**Salida:**

```text
Vi la marca de interrupción y paro
```

!!! warning "No te tragues la excepción"
    Cuando `InterruptedException` salta, **borra la marca**. Si la capturas y sigues como si nada, la petición de parar se pierde. Lo correcto es **terminar** el trabajo o **volver a poner la marca** con `Thread.currentThread().interrupt()` (el [ejercicio 4](ejercicios.md#ejercicio-4) lo practica).

## `stop()` ya no existe

Desde **JDK 20**, `stop()` lanza `UnsupportedOperationException`. Para pedir que un hilo pare, se usa **`interrupt()`**.

```java
public class Parar {
    @SuppressWarnings("removal")
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) { System.out.println("interrumpido"); }
        });
        t.start();
        try {
            t.stop();                                     // ya no existe
        } catch (UnsupportedOperationException e) {
            System.out.println("stop() lanza " + e.getClass().getSimpleName());
        }
        t.interrupt();                                    // esta es la forma correcta de pedir que pare
        t.join();
    }
}
```

**Salida:**

```text
stop() lanza UnsupportedOperationException
interrumpido
```

## Hilos demonio: la JVM no los espera

La JVM termina cuando acaban **todos los hilos normales**. A los **demonios** los corta sin esperar. Sirven para tareas de fondo: autoguardado, limpieza, el recolector de basura. Hay que marcarlos **antes de `start()`**.

<div class="pg" data-pg="demonio" markdown="1">

<div class="pg-panel" data-lang="java" data-forma="funcion" markdown="1">

<p class="pg-cab">Java · como función</p>

```java
public class Main {
    static void autoguardado() {
        while (true) {
            System.out.println("Autoguardado...");
            try { Thread.sleep(300); } catch (InterruptedException e) { return; }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread guardado = new Thread(Main::autoguardado);
        guardado.setDaemon(true);                      // siempre antes de start()
        guardado.start();
        Thread.sleep(1000);
        System.out.println("Fin del main");            // y el programa acaba
    }
}
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="java" data-forma="clase" markdown="1">

<p class="pg-cab">Java · como clase</p>

```java
public class Demo {
    static class Autoguardado extends Thread {
        Autoguardado() { setDaemon(true); }            // demonio desde el constructor, antes de start()

        public void run() {
            while (true) {
                System.out.println("Autoguardado...");
                try { Thread.sleep(300); } catch (InterruptedException e) { return; }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Autoguardado().start();
        Thread.sleep(1000);
        System.out.println("Fin del main");            // y el programa acaba
    }
}
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="funcion" markdown="1">

<p class="pg-cab">Kotlin · como función</p>

```kotlin
import kotlin.concurrent.thread

fun autoguardado() {
    while (true) {
        println("Autoguardado...")
        Thread.sleep(300)
    }
}

fun main() {
    thread(isDaemon = true) { autoguardado() }         // demonio: la JVM no lo espera
    Thread.sleep(1000)
    println("Fin del main")                            // y el programa acaba
}
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="clase" markdown="1">

<p class="pg-cab">Kotlin · como clase</p>

```kotlin
class Autoguardado : Thread() {
    init { isDaemon = true }                           // demonio desde el constructor, antes de start()

    override fun run() {
        while (true) {
            println("Autoguardado...")
            sleep(300)
        }
    }
}

fun main() {
    Autoguardado().start()
    Thread.sleep(1000)
    println("Fin del main")                            // y el programa acaba
}
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="python" data-forma="funcion" markdown="1">

<p class="pg-cab">Python · como función</p>

```python
import threading
import time


def autoguardado():
    while True:
        print("Autoguardado...")
        time.sleep(0.3)


threading.Thread(target=autoguardado, daemon=True).start()     # daemon=True: el programa no lo espera
time.sleep(1)
print("Fin del main")                                          # y el programa acaba
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="python" data-forma="clase" markdown="1">

<p class="pg-cab">Python · como clase</p>

```python
import threading
import time


class Autoguardado(threading.Thread):
    def __init__(self):
        super().__init__(daemon=True)                  # demonio desde el constructor, antes de start()

    def run(self):
        while True:
            print("Autoguardado...")
            time.sleep(0.3)


Autoguardado().start()
time.sleep(1)
print("Fin del main")                                  # y el programa acaba
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="csharp" data-forma="funcion" markdown="1">

<p class="pg-cab">C# · como función</p>

```csharp
Console.OutputEncoding = System.Text.Encoding.UTF8;

void Autoguardado()
{
    while (true)
    {
        Console.WriteLine("Autoguardado...");
        Thread.Sleep(300);
    }
}

var guardado = new Thread(Autoguardado) { IsBackground = true };     // segundo plano: el programa no lo espera
guardado.Start();
Thread.Sleep(1000);
Console.WriteLine("Fin del main");                                   // y el programa acaba
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

<div class="pg-panel" data-lang="csharp" data-forma="clase" markdown="1">

<p class="pg-cab">C# · como clase</p>

```csharp
Console.OutputEncoding = System.Text.Encoding.UTF8;

var guardado = new Thread(new Autoguardado().Ejecutar) { IsBackground = true };
guardado.Start();
Thread.Sleep(1000);
Console.WriteLine("Fin del main");                                   // y el programa acaba

class Autoguardado
{
    public void Ejecutar()
    {
        while (true)
        {
            Console.WriteLine("Autoguardado...");
            Thread.Sleep(300);
        }
    }
}
```

**Salida:**

```text
Autoguardado...
Autoguardado...
Autoguardado...
Autoguardado...
Fin del main
```

</div>

</div>

Sin `setDaemon(true)` (en Python `daemon=True`, en C# `IsBackground = true`), este programa **no terminaría nunca**, porque el hilo de autoguardado nunca acaba. En los demás lenguajes el nombre cambia, pero la idea es la misma.
