# U6.3 · Comentarios y documentación

<div class="ej-gate" data-unit="u06" data-nombre="U6 · Diseño de programas con POO"></div>

Un buen comentario explica el **porqué**, no repite lo que ya dice el código. La documentación de clases y funciones se escribe con el formato de cada lenguaje y se convierte en páginas web con una herramienta.

## Ejercicio 6.11

**Documenta tu código.** Toma las clases de la biblioteca de medios (ejercicios 6.1 y 6.2) y añade documentación a todas las clases públicas y a sus métodos: qué hace, qué recibe, qué devuelve y qué excepciones puede lanzar. Incluye al menos un ejemplo de uso.

!!! note "En Java"
    Comentarios Javadoc `/** ... */` con `@param`, `@return` y `@throws`.

## Ejercicio 6.12

**Genera la documentación.** Ejecuta la herramienta de tu lenguaje para crear la documentación en HTML, ábrela en el navegador y revisa si se entiende sin mirar el código. Corrige lo que no quede claro.

!!! note "En Java"
    Ejecuta `javadoc -d docs src/*.java` (o `mvn javadoc:javadoc`).

## Ejercicio 6.13

**Comentarios que sobran.** Dado este fragmento, elimina los comentarios inútiles, mejora los que aporten algo y renombra lo que haga falta para que el código se explique solo:

```text
// incrementa i
i = i + 1
// si x es mayor que 18
if x > 18:
    // imprime mayor
    print("mayor")
// variable para el precio
p = 10
```
