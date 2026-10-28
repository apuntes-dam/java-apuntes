# 2.5 Hilos virtuales

Desde **Java 21**: hilos **ligeros** que gestiona la JVM. Puedes tener **millones** esperando a la vez.

## Hilo de plataforma frente a hilo virtual

| | **De plataforma** | **Virtual** |
|---|---|---|
| Lo gestiona | El sistema operativo | La JVM |
| Pila | ≈ 1 MB reservado | Unos KB, crece si hace falta |
| Cuántos caben | Miles | Millones |
| Al esperar E/S | Ocupa un hilo del SO | Se **aparca** y libera el hilo real |
| Ideal para | CPU-bound | IO-bound con **muchas tareas** |

Muchos hilos virtuales se reparten unos **pocos hilos reales**, los **portadores**. Como los camareros: no esperan junto a la mesa mientras el cliente decide; atienden otra y vuelven.

!!! note "No hacen el cálculo más rápido"
    Hacen **la espera más barata**. Si la tarea calcula, los núcleos son los mismos y no hay esperas que aprovechar.

## Crear un hilo virtual: igual que siempre

Es un `Thread` normal: `join`, `sleep`, `interrupt` y `getState` funcionan igual. `Thread.ofVirtual()` y `Thread.ofPlatform()` son **constructores con nombre**: se leen mejor que `new Thread`. Hay un atajo: `Thread.startVirtualThread(tarea)`.

```java
public class Virtuales {
    public static void main(String[] args) throws InterruptedException {
        Runnable tarea = () -> System.out.println(Thread.currentThread());
        Thread v = Thread.ofVirtual().name("virtual-1").start(tarea);
        Thread p = Thread.ofPlatform().name("plataforma-1").start(tarea);
        v.join();
        p.join();
        System.out.println("¿v es virtual? " + v.isVirtual());
    }
}
```

**Salida:**

```text
VirtualThread[#36,virtual-1]/runnable@ForkJoinPool-1-worker-1
Thread[#38,plataforma-1,5,main]
¿v es virtual? true
```

La salida del hilo virtual muestra el **portador** que lo ejecuta (`ForkJoinPool-1-worker-…`). Los números de hilo cambian en cada ejecución.

## 10.000 tareas que esperan un segundo

Un **ejecutor** lanza un hilo virtual por cada tarea que le das con `submit`. Con `try-with-resources` no hacen falta `join`: **el cierre espera por ti**.

```java
import java.time.Duration;
import java.util.concurrent.Executors;

public class DiezMil {
    public static void main(String[] args) {
        long ini = System.currentTimeMillis();
        try (var exec = Executors.newVirtualThreadPerTaskExecutor()) {
            for (int i = 0; i < 10_000; i++) {
                exec.submit(() -> {
                    Thread.sleep(Duration.ofSeconds(1));        // simula la red
                    return null;
                });
            }
        }                                                       // al cerrar el try, espera a que acaben todas
        System.out.println("Tiempo: " + (System.currentTimeMillis() - ini) + " ms");
    }
}
```

**Salida:**

```text
Tiempo: 1049 ms
```

Diez mil tareas de un segundo, en **algo más de un segundo en total**: todas esperan a la vez. Con hilos de plataforma harían falta decenas de miles de hilos del sistema.

!!! example "Ejercicio de clase"
    El [ejercicio 5](ejercicios.md#ejercicio-5) compara **100 hilos de plataforma** contra **hilos virtuales** con 1.000 tareas, y qué pasa si las tareas **calculan** en lugar de esperar.
