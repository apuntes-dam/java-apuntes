# PSP · Tema 2 · Multihilos

Los **hilos** son la forma de que un programa haga varias cosas a la vez dentro de un mismo proceso. Este tema es la **sesión 1 de 3** del profesor (curso 2026/27) y se trabaja todo en **Java 21 o superior**: qué es un hilo, cómo se crea, cómo vive, cómo se controla y la novedad de Java 21, los **hilos virtuales**. Las sesiones siguientes (condiciones de carrera y sincronización; cooperación entre hilos y *pools*) se añadirán cuando se impartan.

## Qué hay en esta sección

| Apartado | De qué trata |
|---|---|
| [2.1 Proceso frente a hilo](01-proceso-hilo.md) | Qué comparte un hilo y qué es suyo, para qué sirven y si tu programa es CPU-bound o IO-bound |
| [2.2 Crear hilos](02-crear.md) | `Thread`, `Runnable` y lambda; `start()` frente a `run()`; `join()` y la trampa del bucle |
| [2.3 Ciclo de vida](03-ciclo-vida.md) | Los seis estados de `Thread.State` y qué los provoca |
| [2.4 Controlar hilos](04-controlar.md) | `sleep`, `join`, `interrupt`, hilos demonio y por qué `stop()` ya no existe |
| [2.5 Hilos virtuales](05-virtuales.md) | Hilos ligeros de Java 21: cuándo ganan y cuándo no |
| [2.6 Práctica guiada: gestor de descargas](06-descargas.md) | Cinco descargas en cuatro versiones: de 10 s a 3 s |
| [Ejercicios](ejercicios.md) | Los cinco de clase y dos de ampliación, con solución |

!!! tip "Ejemplos en varios lenguajes"
    Los ejemplos de crear hilos, `start()` frente a `run()`, estados y hilos demonio tienen **pestañas**: Java va abierto por defecto, y con **+** abres **Kotlin**, **Python** o **C#** (donde el concepto existe). Debajo eliges verlo **como función** (la tarea del hilo es una función o lambda) o **como clase** (la tarea es una clase). Cada versión se ha ejecutado de verdad.

## Lo que ya tienes en otras webs

* La comparación **proceso frente a hilo** con ejemplos ejecutados está en [Procesos · 1.4 Hilos, servicios y procesos](https://apuntes-dam.github.io/procesos-apuntes/u01/04-hilos-servicios/).
* `ExecutorService`, `synchronized` y el paso de mensajes están en el modo Avanzado: [A3.A Tareas a la vez](../../avanzado/a3/t-tareas.md) y [A3.C Compartir datos con seguridad](../../avanzado/a3/t-compartir.md). Es justo lo que continúa en las próximas sesiones.

## Lo que hay que llevarse de la sesión

| | |
|---|---|
| **Hilo** | Línea de ejecución dentro de un proceso. Comparte el *heap*; la pila es suya |
| **Crear** | `Runnable` o lambda y `start()`. Nunca llames tú a `run()` |
| **Estados** | `NEW`, `RUNNABLE`, `BLOCKED`, `WAITING`, `TIMED_WAITING`, `TERMINATED` |
| **Esperar** | `join()` espera a otro hilo; `join(ms)` con límite; `sleep(ms)` te duerme a ti |
| **Parar** | `interrupt()` pide parar. No te tragues la `InterruptedException` |
| **Virtuales** | Para muchas tareas que esperan. Para calcular, tantos hilos como núcleos |

!!! info "Qué se ha ejecutado"
    Todos los programas de esta página se han **compilado y ejecutado de verdad** (Java 25 en Windows) al generarla, y lo que ves en «Salida» es lo que sacaron. Cuando intervienen varios hilos, **el orden de las líneas cambia de una ejecución a otra**: es lo normal, y es lo que decide el planificador.

## Antes de pasar a los ejercicios

Cuando hayas leído y probado los ejemplos, marca la casilla para **desbloquear** las soluciones de los [ejercicios](ejercicios.md):

<div class="ej-check" data-unit="psp-t2"></div>
