# 2.2 Crear hilos

La clase `Thread` y la interfaz `Runnable` son del paquete `java.lang`: **no hay que importar nada**.

## Tres formas de decir qué hace un hilo

1. **Heredar de `Thread`** y sobrescribir `run()`.
2. **Implementar `Runnable`** y pasarlo a un `Thread`.
3. **Lambda**: `Runnable` tiene un solo método, así que se puede escribir como una lambda.

En las tres, **el trabajo va en `run()`** y **el hilo arranca con `start()`**.

```java
public class TresFormas {
    static class Cocinero extends Thread {                // 1. heredar de Thread
        public void run() { System.out.println("Cocino"); }
    }

    static class Pinche implements Runnable {             // 2. implementar Runnable
        public void run() { System.out.println("Pico cebolla"); }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread a = new Cocinero();
        Thread b = new Thread(new Pinche());
        Thread c = new Thread(() -> System.out.println("Friego"));      // 3. lambda: Runnable tiene un solo método
        a.start(); a.join();
        b.start(); b.join();
        c.start(); c.join();
    }
}
```

**Salida:**

```text
Cocino
Pico cebolla
Friego
```

| Mejor usar… | Porque… |
|---|---|
| **`Runnable` o lambda** | Separas la **tarea** del **hilo** que la ejecuta, y tu clase puede heredar de otra |
| Heredar de `Thread` | Funciona, pero gastas la **única herencia** que permite Java |

## Crear, arrancar y esperar

Dos hilos con la misma tarea y distinto nombre. `Thread.currentThread()` devuelve **el hilo que está ejecutando esa línea**. Prueba las pestañas: es la misma idea en otros lenguajes, y la forma que elijas decide si la tarea es una **función** o una **clase**.

<div class="pg" data-pg="hola" markdown="1">

<div class="pg-panel" data-lang="java" data-forma="funcion" markdown="1">

<p class="pg-cab">Java · como función</p>

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Runnable saludo = () -> System.out.println("Hola desde " + Thread.currentThread().getName());   // la tarea es una lambda
        Thread t1 = new Thread(saludo, "T1");          // tarea + nombre
        Thread t2 = new Thread(saludo, "T2");
        t1.start(); t2.start();                        // arrancan a la vez
        t1.join(); t2.join();                          // main espera a ambos
        System.out.println("Fin del main");
    }
}
```

**Salida:**

```text
Hola desde T2
Hola desde T1
Fin del main
```

</div>

<div class="pg-panel" data-lang="java" data-forma="clase" markdown="1">

<p class="pg-cab">Java · como clase</p>

```java
public class HolaHilos {
    static class Saludo implements Runnable {          // la tarea es una clase con run()
        public void run() {
            System.out.println("Hola desde " + Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Runnable saludo = new Saludo();
        Thread t1 = new Thread(saludo, "T1");
        Thread t2 = new Thread(saludo, "T2");
        t1.start(); t2.start();
        t1.join(); t2.join();
        System.out.println("Fin del main");
    }
}
```

**Salida:**

```text
Hola desde T1
Hola desde T2
Fin del main
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="funcion" markdown="1">

<p class="pg-cab">Kotlin · como función</p>

```kotlin
fun main() {
    val saludo = Runnable { println("Hola desde ${Thread.currentThread().name}") }     // la tarea es una lambda
    val t1 = Thread(saludo, "T1")                      // tarea + nombre
    val t2 = Thread(saludo, "T2")
    t1.start(); t2.start()                             // arrancan a la vez
    t1.join(); t2.join()                               // main espera a ambos
    println("Fin del main")
}
```

**Salida:**

```text
Hola desde T2
Hola desde T1
Fin del main
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="clase" markdown="1">

<p class="pg-cab">Kotlin · como clase</p>

```kotlin
class Saludo : Runnable {                               // la tarea es una clase con run()
    override fun run() {
        println("Hola desde ${Thread.currentThread().name}")
    }
}

fun main() {
    val saludo = Saludo()
    val t1 = Thread(saludo, "T1")
    val t2 = Thread(saludo, "T2")
    t1.start(); t2.start()
    t1.join(); t2.join()
    println("Fin del main")
}
```

**Salida:**

```text
Hola desde T1
Hola desde T2
Fin del main
```

</div>

<div class="pg-panel" data-lang="python" data-forma="funcion" markdown="1">

<p class="pg-cab">Python · como función</p>

```python
import threading


def saludar():                                         # la tarea es una función
    print(f"Hola desde {threading.current_thread().name}")


t1 = threading.Thread(target=saludar, name="T1")       # tarea + nombre
t2 = threading.Thread(target=saludar, name="T2")
t1.start()
t2.start()                                             # arrancan a la vez
t1.join()
t2.join()                                              # el programa principal espera a ambos
print("Fin del main")
```

**Salida:**

```text
Hola desde T1
Hola desde T2
Fin del main
```

</div>

<div class="pg-panel" data-lang="python" data-forma="clase" markdown="1">

<p class="pg-cab">Python · como clase</p>

```python
import threading


class Saludo(threading.Thread):                        # la tarea es una clase con run()
    def run(self):
        print(f"Hola desde {self.name}")


t1 = Saludo(name="T1")
t2 = Saludo(name="T2")
t1.start()
t2.start()
t1.join()
t2.join()
print("Fin del main")
```

**Salida:**

```text
Hola desde T1
Hola desde T2
Fin del main
```

</div>

<div class="pg-panel" data-lang="csharp" data-forma="funcion" markdown="1">

<p class="pg-cab">C# · como función</p>

```csharp
Console.OutputEncoding = System.Text.Encoding.UTF8;

void Saludar() => Console.WriteLine("Hola desde " + Thread.CurrentThread.Name);      // la tarea es una función

var t1 = new Thread(Saludar) { Name = "T1" };          // tarea + nombre
var t2 = new Thread(Saludar) { Name = "T2" };
t1.Start(); t2.Start();                                // arrancan a la vez
t1.Join(); t2.Join();                                  // Main espera a ambos
Console.WriteLine("Fin del main");
```

**Salida:**

```text
Hola desde T1
Hola desde T2
Fin del main
```

</div>

<div class="pg-panel" data-lang="csharp" data-forma="clase" markdown="1">

<p class="pg-cab">C# · como clase</p>

```csharp
Console.OutputEncoding = System.Text.Encoding.UTF8;

var saludo = new Saludo();
var t1 = new Thread(saludo.Run) { Name = "T1" };
var t2 = new Thread(saludo.Run) { Name = "T2" };
t1.Start(); t2.Start();
t1.Join(); t2.Join();
Console.WriteLine("Fin del main");

class Saludo                                           // la tarea es un método de una clase
{
    public void Run() => Console.WriteLine("Hola desde " + Thread.CurrentThread.Name);
}
```

**Salida:**

```text
Hola desde T1
Hola desde T2
Fin del main
```

</div>

</div>

* **El orden de T1 y T2 cambia** entre ejecuciones: lo decide el planificador.
* Sin los `join()`, «Fin del main» podría salir **antes** que los saludos.

## El error clásico: llamar a `run()` en vez de `start()`

| | Qué hace |
|---|---|
| **`run()`** | Lo que hace el hilo. **Nunca lo llames tú**: se ejecuta en tu propio hilo, como un método normal |
| **`start()`** | Pide al sistema un hilo nuevo y **él llama a `run()`** |

<div class="pg" data-pg="run_start" markdown="1">

<div class="pg-panel" data-lang="java" data-forma="funcion" markdown="1">

<p class="pg-cab">Java · como función</p>

```java
public class Main {
    static void tarea() {
        System.out.println("Me ejecuta: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(Main::tarea, "Worker");
        t.run();                       // NO crea hilo: lo ejecuta main, como un método normal
        t.start();                     // SÍ crea un hilo nuevo que ejecuta run()
        t.join();
    }
}
```

**Salida:**

```text
Me ejecuta: main
Me ejecuta: Worker
```

</div>

<div class="pg-panel" data-lang="java" data-forma="clase" markdown="1">

<p class="pg-cab">Java · como clase</p>

```java
public class Demo {
    static class Tarea implements Runnable {
        public void run() {
            System.out.println("Me ejecuta: " + Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(new Tarea(), "Worker");
        t.run();                       // NO crea hilo: lo ejecuta main, como un método normal
        t.start();                     // SÍ crea un hilo nuevo que ejecuta run()
        t.join();
    }
}
```

**Salida:**

```text
Me ejecuta: main
Me ejecuta: Worker
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="funcion" markdown="1">

<p class="pg-cab">Kotlin · como función</p>

```kotlin
fun tarea() {
    println("Me ejecuta: ${Thread.currentThread().name}")
}

fun main() {
    val t = Thread(::tarea, "Worker")
    t.run()                            // NO crea hilo: lo ejecuta main, como un método normal
    t.start()                          // SÍ crea un hilo nuevo que ejecuta run()
    t.join()
}
```

**Salida:**

```text
Me ejecuta: main
Me ejecuta: Worker
```

</div>

<div class="pg-panel" data-lang="kotlin" data-forma="clase" markdown="1">

<p class="pg-cab">Kotlin · como clase</p>

```kotlin
class Tarea : Runnable {
    override fun run() {
        println("Me ejecuta: ${Thread.currentThread().name}")
    }
}

fun main() {
    val t = Thread(Tarea(), "Worker")
    t.run()                            // NO crea hilo: lo ejecuta main, como un método normal
    t.start()                          // SÍ crea un hilo nuevo que ejecuta run()
    t.join()
}
```

**Salida:**

```text
Me ejecuta: main
Me ejecuta: Worker
```

</div>

<div class="pg-panel" data-lang="python" data-forma="funcion" markdown="1">

<p class="pg-cab">Python · como función</p>

```python
import threading


def tarea():
    print("Me ejecuta:", threading.current_thread().name)


hilo1 = threading.Thread(target=tarea, name="Worker")
hilo1.run()                            # NO crea hilo: lo ejecuta el programa principal, como una función normal
hilo2 = threading.Thread(target=tarea, name="Worker")      # (tras run() el objeto ya no vale: se crea otro)
hilo2.start()                          # SÍ crea un hilo nuevo que ejecuta run()
hilo2.join()
```

**Salida:**

```text
Me ejecuta: MainThread
Me ejecuta: Worker
```

</div>

<div class="pg-panel" data-lang="python" data-forma="clase" markdown="1">

<p class="pg-cab">Python · como clase</p>

```python
import threading


class Tarea(threading.Thread):
    def run(self):
        print("Me ejecuta:", threading.current_thread().name)


hilo1 = Tarea(name="Worker")
hilo1.run()                            # NO crea hilo: lo ejecuta el programa principal, como un método normal
hilo2 = Tarea(name="Worker")
hilo2.start()                          # SÍ crea un hilo nuevo que ejecuta run()
hilo2.join()
```

**Salida:**

```text
Me ejecuta: MainThread
Me ejecuta: Worker
```

</div>

</div>

La primera línea la imprime `main` (el hilo que llamó a `run()`); la segunda, el hilo `Worker`. Y `start()` **solo se puede llamar una vez**:

```java
public class StartDosVeces {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> System.out.println("Corro"));
        t.start();
        t.join();
        try {
            t.start();                                    // un hilo solo se arranca una vez
        } catch (IllegalThreadStateException e) {
            System.out.println("Segundo start(): " + e);
        }
    }
}
```

**Salida:**

```text
Corro
Segundo start(): java.lang.IllegalThreadStateException
```

## Muchos hilos: un bucle para arrancar, otro para esperar

Guarda los hilos en una **lista o un array** para poder esperarlos después. Dentro de la lambda **no puedes usar `i`** porque cambia en cada vuelta: por eso se copia en `n`.

```java
import java.util.ArrayList;
import java.util.List;

public class MuchosHilos {
    public static void main(String[] args) throws InterruptedException {
        List<Thread> hilos = new ArrayList<>();
        for (int i = 0; i < 8; i++) {
            int n = i;                                    // la lambda necesita una variable que no cambie
            Thread t = new Thread(() -> System.out.println("Hola desde H" + n), "H" + n);
            hilos.add(t);
            t.start();
        }
        for (Thread t : hilos) {
            t.join();                                     // esperar a todos, después
        }
        System.out.println("Todos han terminado");
    }
}
```

**Salida:**

```text
Hola desde H3
Hola desde H6
Hola desde H2
Hola desde H7
Hola desde H0
Hola desde H1
Hola desde H4
Hola desde H5
Todos han terminado
```

!!! warning "La trampa"
    Si haces `join()` **dentro del primer bucle**, cada hilo espera al anterior y vuelves a lo **secuencial**. Se nota en el tiempo (cuatro hilos de 200 ms cada uno):

```java
public class TrampaJoin {
    static Thread nuevo() {
        return new Thread(() -> {
            try { Thread.sleep(200); } catch (InterruptedException e) { }
        });
    }

    public static void main(String[] args) throws InterruptedException {
        long ini = System.currentTimeMillis();
        for (int i = 0; i < 4; i++) {
            Thread t = nuevo();
            t.start();
            t.join();                                     // MAL: cada hilo espera al anterior
        }
        System.out.println("join dentro del primer bucle: " + (System.currentTimeMillis() - ini) + " ms");

        ini = System.currentTimeMillis();
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = nuevo();
            hilos[i].start();
        }
        for (Thread t : hilos) t.join();                  // BIEN: arrancar todos y esperar después
        System.out.println("join en un segundo bucle:      " + (System.currentTimeMillis() - ini) + " ms");
    }
}
```

**Salida:**

```text
join dentro del primer bucle: 810 ms
join en un segundo bucle:      201 ms
```

!!! example "Ejercicio de clase"
    El [ejercicio 2](ejercicios.md#ejercicio-2) es la cocina con tres cocineros: crear y arrancar en un bucle, esperar en otro.
