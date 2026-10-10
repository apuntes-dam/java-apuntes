# A6 · Procesos desde Java

Crear y controlar **procesos desde el propio código**: lanzar otro programa con **`ProcessBuilder`**, recoger su **código de salida**, **leer su salida y enviarle datos**, redirigirla a ficheros, poner **límites de tiempo**, detenerlo y observar procesos con **`ProcessHandle`**. Es la práctica de la teoría de la [web de Procesos y planificación de la CPU](https://apuntes-dam.github.io/procesos-apuntes/).

## Teoría

* [A6.A Lanzar un proceso](t-lanzar.md): `ProcessBuilder`, `Process`, el comando troceado, la carpeta, el entorno y el código de salida.
* [A6.B Hablar con el proceso hijo](t-flujos.md): leer su salida, enviarle datos, redirecciones y las trampas típicas.
* [A6.C Controlar y observar procesos](t-controlar.md): esperar con límite, detenerlo y `ProcessHandle`.

## Antes de empezar: qué debes dominar

* Excepciones y ficheros (unidades 2 y 7).
* Qué es un proceso, su PID y sus estados (web de Procesos, unidad 1).
* Funciones como valores, para `onExit()` (unidad A1 de este apartado).

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [A6 · Ejercicios de procesos](ejercicios.md) | 6 |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="a6"></div>
