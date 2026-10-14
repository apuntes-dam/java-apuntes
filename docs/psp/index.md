# PSP · Tema 1: procesos en Java

Esta sección acompaña al **Tema 1 (Multiprocesos)** del módulo *Programación de Servicios y Procesos* de 2.º DAM. La teoría y el boletín del módulo están en [la web del módulo](https://joselejimenez.github.io/PSP/) (es del profesorado, no de esta web). Aquí no se repite la teoría: se indica **dónde estudiar cada apartado** en las webs de apuntes y se añade un **simulador de examen** con la parte de Java.

## Dónde estudiar cada apartado

| Apartado del Tema 1 | Dónde estudiarlo |
|---|---|
| 1. Programa, proceso, servicio e hilo | [Procesos · 1.1 Qué es un proceso](https://apuntes-dam.github.io/procesos-apuntes/u01/01-proceso/) y [1.4 Hilos, servicios y procesos huérfanos o zombis](https://apuntes-dam.github.io/procesos-apuntes/u01/04-hilos-servicios/) |
| 2. Concurrente, paralela y distribuida (Amdahl) | [Procesos · 1.5 Concurrencia, paralelismo y distribución](https://apuntes-dam.github.io/procesos-apuntes/u01/05-concurrencia/) |
| 3. Estados de un proceso | [Procesos · 1.2 Estados y transiciones](https://apuntes-dam.github.io/procesos-apuntes/u01/02-estados/) |
| 4. Planificación de la CPU | [Procesos · unidad 2](https://apuntes-dam.github.io/procesos-apuntes/u02/) y su [simulador de examen](https://apuntes-dam.github.io/procesos-apuntes/simulador-examen/) |
| 5. Crear procesos en Java | [A6.A Lanzar un proceso](../avanzado/a6/t-lanzar.md) |
| 6. Comunicar y controlar procesos | [A6.B Hablar con el proceso hijo](../avanzado/a6/t-flujos.md) y [A6.C Controlar y observar procesos](../avanzado/a6/t-controlar.md) |

!!! note "La unidad A6 está en el modo Avanzado"
    La unidad **A6 · Procesos desde Java** solo aparece en el menú con el interruptor **Avanzado** de la cabecera activado. Sus enlaces de arriba funcionan igual.

## Simulador de examen

El [simulador](simulador.md) practica lo que es de **Java**: crear procesos con `ProcessBuilder`, el código de salida, leer y enviar datos al hijo, redirecciones, tuberías, esperar con límite y terminar procesos, y `ProcessHandle`. Para la teoría de procesos, estados y planificación (con cálculos), usa el simulador de la web de Procesos.
