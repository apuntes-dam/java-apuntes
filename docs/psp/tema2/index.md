# PSP · Tema 2 · Multihilos

Los **hilos** son la forma de que un programa haga varias cosas a la vez dentro de un mismo proceso. Este tema junta las **sesiones 1 y 2** del profesor (curso 2026/27) y se trabaja todo en **Java 21 o superior**: qué es un hilo, cómo se crea, cómo vive, cómo se controla, los **hilos virtuales**, y qué pasa cuando varios hilos tocan el mismo dato (**condiciones de carrera**, `synchronized` y **clases atómicas**).

## Qué hay en esta sección

| Apartado | De qué trata |
|---|---|
| [2.1 Proceso frente a hilo](01-proceso-hilo.md) | Qué comparte un hilo y qué es suyo, para qué sirven y si tu programa es CPU-bound o IO-bound |
| [2.2 Crear hilos](02-crear.md) | `Thread`, `Runnable` y lambda; `start()` frente a `run()`; `join()` y la trampa del bucle |
| [2.3 Ciclo de vida](03-ciclo-vida.md) | Los seis estados de `Thread.State` y qué los provoca |
| [2.4 Controlar hilos](04-controlar.md) | `sleep`, `join`, `interrupt`, hilos demonio y por qué `stop()` ya no existe |
| [2.5 Hilos virtuales](05-virtuales.md) | Hilos ligeros de Java 21: cuándo ganan y cuándo no |
| [2.6 Práctica guiada: gestor de descargas](06-descargas.md) | Cinco descargas en cuatro versiones: de 10 s a 3 s |
| [2.7 Condición de carrera](07-carrera.md) | Por qué falla un programa cuando dos hilos escriben en el mismo dato, sin dar ningún error |
| [2.8 synchronized](08-synchronized.md) | Exclusión mutua: que solo un hilo a la vez entre en la sección crítica |
| [2.9 Clases atómicas](09-atomicas.md) | `AtomicInteger` y cuándo conviene más que `synchronized` |
| [Ejercicios](ejercicios.md) | Los de las dos sesiones: 7 de clase/ampliación y el boletín de 5 más, con solución |

!!! tip "Ejemplos en varios lenguajes"
    Los ejemplos de crear hilos, `start()` frente a `run()`, estados y hilos demonio tienen **pestañas**: Java va abierto por defecto, y con **+** abres **Kotlin**, **Python** o **C#** (donde el concepto existe). Debajo eliges verlo **como función** (la tarea del hilo es una función o lambda) o **como clase** (la tarea es una clase). Cada versión se ha ejecutado de verdad.

## Lo que ya tienes en otras webs

* La comparación **proceso frente a hilo** con ejemplos ejecutados está en [Procesos · 1.4 Hilos, servicios y procesos](https://apuntes-dam.github.io/procesos-apuntes/u01/04-hilos-servicios/).
* `ExecutorService` y el paso de mensajes están en el modo Avanzado: [A3.A Tareas a la vez](../../avanzado/a3/t-tareas.md) y [A3.C Compartir datos con seguridad](../../avanzado/a3/t-compartir.md), que ya usa `synchronized` — ahora, con los apartados 2.7 a 2.9, tienes la base para entenderlo del todo.

## Lo que hay que llevarse de las dos sesiones

| | |
|---|---|
| **Hilo** | Línea de ejecución dentro de un proceso. Comparte el *heap*; la pila es suya |
| **Crear** | `Runnable` o lambda y `start()`. Nunca llames tú a `run()` |
| **Estados** | `NEW`, `RUNNABLE`, `BLOCKED`, `WAITING`, `TIMED_WAITING`, `TERMINATED` |
| **Esperar** | `join()` espera a otro hilo; `join(ms)` con límite; `sleep(ms)` te duerme a ti |
| **Parar** | `interrupt()` pide parar. No te tragues la `InterruptedException` |
| **Virtuales** | Para muchas tareas que esperan. Para calcular, tantos hilos como núcleos |
| **Condición de carrera** | Varios hilos + un dato compartido + al menos uno escribe = resultado impredecible |
| **synchronized** | Un único cerrojo compartido protege la sección crítica; los demás esperan en `BLOCKED` |
| **Clase atómica** | Para una sola variable y una sola operación; no bloquea nadie |

!!! info "Qué se ha ejecutado"
    Todos los programas de esta página se han **compilado y ejecutado de verdad** (Java 25 en Windows) al generarla, y lo que ves en «Salida» es lo que sacaron. Cuando intervienen varios hilos, **el orden de las líneas cambia de una ejecución a otra**: es lo normal, y es lo que decide el planificador.

## Antes de pasar a los ejercicios

Cuando hayas leído y probado los ejemplos, marca la casilla para **desbloquear** las soluciones de los [ejercicios](ejercicios.md):

<div class="ej-check" data-unit="psp-t2"></div>
