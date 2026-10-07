# A3.A Tareas a la vez

!!! info "Para quién es esto"
    Es material **avanzado**: da por sabidas las unidades 1 a 5 y la [unidad A1 de funciones como valores](../a1/index.md), porque las tareas se pasan como funciones.

Un programa **concurrente** tiene varias tareas **en marcha a la vez**. No siempre significa ejecutar a la vez de verdad: un solo procesador puede **alternar** entre tareas tan rápido que parece simultáneo. Cuando varias tareas se ejecutan **realmente** a la vez en varios núcleos, se habla de **paralelismo**.

| Tipo de tarea | Qué hace casi todo el tiempo | Ejemplos | Qué ayuda |
|---|---|---|---|
| **De espera** (*I/O-bound*) | **Esperar** a algo externo | Descargar, consultar una base de datos, leer un archivo | Alternar tareas (concurrencia) |
| **De cálculo** (*CPU-bound*) | **Calcular** sin parar | Comprimir, procesar una imagen, recorrer millones de datos | Repartir entre núcleos (paralelismo) |

## El modelo de Java

Java trabaja con **hilos** (`Thread`) del sistema operativo. Crear uno por tarea es caro, así que se usa un **grupo de hilos** (`ExecutorService`) que los reutiliza: le entregas tareas con `submit` y te devuelve un `Future` con el resultado futuro. Desde **Java 21** existen además los **hilos virtuales** (*virtual threads*), muy baratos, pensados para miles de tareas que esperan (red, bases de datos). Y `CompletableFuture` permite encadenar tareas asíncronas.

## Un ejemplo: tres tareas a la vez

Tres tareas simulan descargas de distinta duración (300, 100 y 200 ms). Se lanzan **todas a la vez**, se espera a que acaben y se comprueba el tiempo total:

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

// Tres tareas que "esperan" (como una descarga) y avanzan a la vez, cada una en su hilo del grupo.
public class Tareas {
    static void tarea(String nombre, int ms) {
        try {
            Thread.sleep(ms);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println("termina " + nombre);
    }

    public static void main(String[] args) throws Exception {
        long inicio = System.nanoTime();
        String[] nombres = {"A", "B", "C"};
        int[] tiempos = {300, 100, 200};

        ExecutorService grupo = Executors.newFixedThreadPool(3); // un grupo de 3 hilos reutilizables
        List<Future<?>> tareas = new ArrayList<>();
        for (int i = 0; i < nombres.length; i++) {
            String nombre = nombres[i];
            int ms = tiempos[i];
            System.out.println("empieza " + nombre);
            tareas.add(grupo.submit(() -> tarea(nombre, ms))); // se lanza y todavía NO se espera
        }
        for (Future<?> tarea : tareas) {
            tarea.get(); // ahora sí: esperar a cada una
        }
        grupo.shutdown(); // sin esto el programa no termina: los hilos del grupo siguen vivos

        long ms = (System.nanoTime() - inicio) / 1_000_000;
        System.out.println("las tres tareas juntas tardaron menos de 450 ms: " + (ms < 450 ? "sí" : "no"));
    }
}
```

Salida:

```text
empieza A
empieza B
empieza C
termina B
termina C
termina A
las tres tareas juntas tardaron menos de 450 ms: sí
```

`Thread.sleep` **bloquea ese hilo**, pero como cada tarea tiene el suyo, las demás siguen. Con un grupo de 3 hilos, las 3 tareas avanzan a la vez.

Fíjate en dos cosas de la salida:

1. **Empiezan todas antes de que termine ninguna.** Se lanzan sin esperar.
2. **Terminan por duración, no por orden de lanzamiento**: B (100 ms), luego C (200 ms) y por último A (300 ms). El tiempo total es el de la **más lenta**, unos 300 ms, no la suma de las tres (600 ms).

!!! warning "Un orden que depende del tiempo"
    El orden en que terminan las tareas **no está garantizado por el lenguaje**: depende de cuánto tarden de verdad. En este ejemplo las pausas están muy separadas para que el resultado sea siempre el mismo. En un programa real, no escribas código que dependa de qué tarea termina antes.

## Qué usar en cada caso

| Si necesitas | En Java |
|---|---|
| Muchas tareas, con cálculo | `ExecutorService` con hilos de plataforma |
| Miles de tareas que esperan | Hilos virtuales (Java 21+) |
| Encadenar pasos asíncronos | `CompletableFuture` |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Bloquear con una espera normal (`sleep`) dentro de una tarea asíncrona | Usa la espera propia del modelo (`delay`, `asyncio.sleep`, `Future.delayed`) |
| Lanzar una tarea y no esperarla | Guarda el `Future`/`Job`/tarea y espera a que termine |
| Dar por hecho el orden en que terminan | Si importa el orden, espera a cada una por separado |
| Crear un hilo por cada tarea pequeña | Usa un grupo de hilos o corrutinas |

## Para practicar

El ejercicio [A3.1](ejercicios.md) pide lanzar tres descargas a la vez, y el [A3.2](ejercicios.md) repartir una suma entre dos tareas. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
