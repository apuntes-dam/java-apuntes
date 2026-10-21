# PSP · Repaso de Java y procesos

Esta sección acompaña al módulo *Programación de Servicios y Procesos* de 2.º DAM, en los temas que ya se han impartido: el **Tema 0 (Repaso de Java)** y el **Tema 1 (Multiprocesos)**. La teoría y los boletines del módulo están en [la web del módulo](https://joselejimenez.github.io/PSP/) (es del profesorado, no de esta web). Aquí no se repite la teoría: se indica **dónde estudiar cada apartado** en las webs de apuntes, se añade lo que faltaba y hay un **simulador de examen** tipo test.

## Tema 0 · Repaso de Java

Casi todo el Java de 1.º DAM que pide el tema está en las unidades de esta web. La página [Lo que completa el repaso](tema0.md) tiene la tabla **apartado por apartado** y añade lo que las unidades no detallan (`varargs`, recursión, trampas de arrays y de `Scanner`, `yield`, `equals` y `hashCode`, formato de texto y lectura de trazas).

## Tema 1 · Multiprocesos

### Dónde estudiar cada apartado

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

El [simulador](simulador.md) tiene dos temas. El **Tema 0** (repaso de Java) es un examen **tipo test**: preguntas de teoría, «qué muestra este programa» y completar una línea. El **Tema 1** practica lo que es de **Java**: crear procesos con `ProcessBuilder`, el código de salida, leer y enviar datos al hijo, redirecciones, tuberías, esperar con límite y terminar procesos, y `ProcessHandle`. Para la teoría de procesos, estados y planificación (con cálculos), usa el simulador de la web de Procesos.
