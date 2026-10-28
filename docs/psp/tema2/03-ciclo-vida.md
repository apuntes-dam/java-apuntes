# 2.3 Ciclo de vida de un hilo

Un hilo pasa por **seis estados**, definidos en el `enum` **`Thread.State`**, según lo que hacemos nosotros y lo que decide el sistema.

| Estado | Significa |
|---|---|
| `NEW` | Creado con `new Thread(...)`, todavía sin `start()` |
| `RUNNABLE` | Listo o ejecutándose (tras `start()`) |
| `BLOCKED` | Espera un **cerrojo ocupado** (`synchronized`; es de la próxima sesión) |
| `WAITING` | Espera **sin límite de tiempo**: `join()` o `wait()` |
| `TIMED_WAITING` | Espera **con límite**: `sleep(ms)` o `join(ms)`; vuelve solo |
| `TERMINATED` | Terminó `run()` (o lanzó una excepción) |

## Qué provoca cada cambio de estado

| Lo que ocurre | Estado del hilo |
|---|---|
| `new Thread(tarea)` | `NEW` |
| `t.start()` | `RUNNABLE` |
| `Thread.sleep(500)` | `TIMED_WAITING` |
| `otro.join()` | `WAITING` |
| `otro.join(2000)` | `TIMED_WAITING` |
| Entrar en un `synchronized` ocupado | `BLOCKED` |
| Esperar el teclado o la red | **`RUNNABLE`** (¡ojo!) |
| Acaba `run()` o lanza una excepción | `TERMINATED` |

## Ver los estados con `getState()`

Consulta el estado con `t.getState()` o pregunta `t.isAlive()`, que es `true` si el hilo ya arrancó y no ha terminado. Este ejemplo recorre cuatro estados; en **C#** los nombres son los de `ThreadState` (`Unstarted`, `Running`, `WaitSleepJoin`, `Stopped`): misma idea, otros nombres. En Python no hay un equivalente a `getState()`.

<div class="pg" data-pg="estados" markdown="1">

<div class="pg-panel" data-lang="java" data-forma="funcion" markdown="1">

<p class="pg-cab">Java · como función</p>

```java
public class Main {
    static void dormir() {
        try { Thread.sleep(500); } catch (InterruptedException e) { }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(Main::dormir);
        System.out.println("1: " + t.getState());
        t.start();
        System.out.println("2: " + t.getState());
        Thread.sleep(100);                             // damos tiempo a que se duerma
        System.out.println("3: " + t.getState());
        t.join();
        System.out.println("4: " + t.getState());
    }
}
```

**Salida:**

```text
1: NEW
2: RUNNABLE
3: TIMED_WAITING
4: TERMINATED
```

</div>

<div class="pg-panel" data-lang="java" data-forma="clase" markdown="1">

<p class="pg-cab">Java · como clase</p>

```java
public class Demo {
    static class Durmiente implements Runnable {
        public void run() {
            try { Thread.sleep(500); } catch (InterruptedException e) { }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(new Durmiente());
        System.out.println("1: " + t.getState());
        t.start();
        System.out.println("2: " + t.getState());
        Thread.sleep(100);                             // damos tiempo a que se duerma
        System.out.println("3: " + t.getState());
        t.join();
        System.out.println("4: " + t.getState());
    }
}
```

**Salida:**

```text
1: NEW
2: RUNNABLE
3: TIMED_WAITING
4: TERMINATED
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="funcion" markdown="1">

<p class="pg-cab">Kotlin · como función</p>

```kotlin
fun dormir() {
    try { Thread.sleep(500) } catch (e: InterruptedException) { }
}

fun main() {
    val t = Thread(::dormir)
    println("1: ${t.state}")
    t.start()
    println("2: ${t.state}")
    Thread.sleep(100)                                  // damos tiempo a que se duerma
    println("3: ${t.state}")
    t.join()
    println("4: ${t.state}")
}
```

**Salida:**

```text
1: NEW
2: RUNNABLE
3: TIMED_WAITING
4: TERMINATED
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="clase" markdown="1">

<p class="pg-cab">Kotlin · como clase</p>

```kotlin
class Durmiente : Runnable {
    override fun run() {
        try { Thread.sleep(500) } catch (e: InterruptedException) { }
    }
}

fun main() {
    val t = Thread(Durmiente())
    println("1: ${t.state}")
    t.start()
    println("2: ${t.state}")
    Thread.sleep(100)                                  // damos tiempo a que se duerma
    println("3: ${t.state}")
    t.join()
    println("4: ${t.state}")
}
```

**Salida:**

```text
1: NEW
2: RUNNABLE
3: TIMED_WAITING
4: TERMINATED
```

</div>

<div class="pg-panel" data-lang="csharp" data-forma="funcion" markdown="1">

<p class="pg-cab">C# · como función</p>

```csharp
Console.OutputEncoding = System.Text.Encoding.UTF8;

void Dormir() => Thread.Sleep(500);

var t = new Thread(Dormir);
Console.WriteLine("1: " + t.ThreadState);
t.Start();
Console.WriteLine("2: " + t.ThreadState);
Thread.Sleep(100);                                     // damos tiempo a que se duerma
Console.WriteLine("3: " + t.ThreadState);
t.Join();
Console.WriteLine("4: " + t.ThreadState);
```

**Salida:**

```text
1: Unstarted
2: Running
3: WaitSleepJoin
4: Stopped
```

</div>

<div class="pg-panel" data-lang="csharp" data-forma="clase" markdown="1">

<p class="pg-cab">C# · como clase</p>

```csharp
Console.OutputEncoding = System.Text.Encoding.UTF8;

var t = new Thread(new Durmiente().Ejecutar);
Console.WriteLine("1: " + t.ThreadState);
t.Start();
Console.WriteLine("2: " + t.ThreadState);
Thread.Sleep(100);                                     // damos tiempo a que se duerma
Console.WriteLine("3: " + t.ThreadState);
t.Join();
Console.WriteLine("4: " + t.ThreadState);

class Durmiente
{
    public void Ejecutar() => Thread.Sleep(500);
}
```

**Salida:**

```text
1: Unstarted
2: Running
3: WaitSleepJoin
4: Stopped
```

</div>

</div>

!!! warning "`getState()` es una foto"
    Cuando la lees, el hilo **ya puede haber cambiado**. Úsalo para **aprender y depurar**, no para decidir qué hace el programa.

## Esperar a otro hilo es `WAITING`, no `BLOCKED`

Es una confusión muy común: un hilo que espera dentro de `otro.join()` está en **`WAITING`** (o `TIMED_WAITING` si `join` lleva límite). **`BLOCKED`** es solo para esperar la entrada a un bloque `synchronized`.

!!! example "Ejercicio de clase"
    El [ejercicio 3](ejercicios.md#ejercicio-3) pide **predecir** los estados A, B, C y D antes de ejecutar, y ver qué cambia con `join(5000)`.
