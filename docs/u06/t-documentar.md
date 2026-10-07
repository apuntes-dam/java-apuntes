# 6.D Comentarios y documentación

Hay dos formas distintas de explicar el código, y conviene no mezclarlas:

* **Comentarios** (`//`, `#`): notas **para quien lea el código**, que explican cosas puntuales del interior.
* **Documentación** (comentarios especiales): descripción **de las clases y funciones públicas para quien las vaya a usar**, sin necesidad de leer su código. Una herramienta la convierte en páginas web.

## Un buen comentario explica el porqué

El código ya dice **qué** hace; un comentario solo aporta valor si cuenta lo que el código **no** puede decir: **por qué** se hizo así.

| Comentario | Valoración |
|---|---|
| `i = i + 1  // suma 1 a i` | **Sobra**: repite el código |
| `// bucle for` | **Sobra**: se ve a simple vista |
| `// la API devuelve las fechas en UTC: hay que convertir antes de comparar` | **Útil**: explica una razón que no se ve |
| `// TODO: cambiar cuando exista el servicio de pagos` | **Útil**, si se revisa y se acaba borrando |
| `// precio = precio * 1.1` (código desactivado) | **Sobra**: bórralo; el control de versiones ya guarda el historial |

!!! tip "Antes de comentar, mejora el nombre"
    Si necesitas un comentario para explicar qué es `p`, quizá lo que falta es llamarla `precioConIva`. Un código con buenos nombres y funciones cortas se explica casi solo, y los comentarios se reservan para el **porqué**.

## Documentar clases y funciones

Una buena documentación de una clase o función pública responde a cuatro preguntas: **qué hace**, **qué recibe**, **qué devuelve** y **qué errores puede dar**; y, si ayuda, un **ejemplo de uso**.

```java
/**
 * Convierte temperaturas entre grados Celsius y Fahrenheit.
 *
 * <p>Ejemplo: {@code new Conversor().celsiusAFahrenheit(100)} devuelve {@code 212}.
 */
public class Conversor {
    /**
     * Devuelve la temperatura en grados Fahrenheit equivalente a una en Celsius.
     *
     * @param celsius temperatura en grados Celsius
     * @return la temperatura en grados Fahrenheit
     * @throws IllegalArgumentException si {@code celsius} está por debajo del cero absoluto (-273 °C)
     */
    public int celsiusAFahrenheit(int celsius) {
        if (celsius < -273) {
            throw new IllegalArgumentException("por debajo del cero absoluto");
        }
        return celsius * 9 / 5 + 32;
    }

    public static void main(String[] args) {
        Conversor conversor = new Conversor();
        for (int c : new int[] {100, 0, -40}) {
            System.out.println(c + " °C = " + conversor.celsiusAFahrenheit(c) + " °F");
        }
        try {
            conversor.celsiusAFahrenheit(-300);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

Salida:

```text
100 °C = 212 °F
0 °C = 32 °F
-40 °C = -40 °F
Error: por debajo del cero absoluto
```

## Cómo se hace en Java

| Necesito... | En Java |
|---|---|
| Comentario de documentación | `/** ... */` justo antes de la clase o del miembro (**Javadoc**) |
| Etiquetas | `@param`, `@return`, `@throws`, `@see`, `@author` |
| Código dentro del texto | `{@code ...}`; el HTML se admite (`<p>`, `<ul>`...) |
| Ver la documentación en el IDE | colocar el cursor sobre el método y pulsar `Ctrl+Q` (IntelliJ) |
| Generar la documentación en HTML | `javadoc -d docs -encoding UTF-8 -docencoding UTF-8 -charset UTF-8 Conversor.java` (crea la carpeta `docs`) |

He comprobado que `javadoc` genera la documentación a partir de este ejemplo; avisa de los métodos o constructores que **no** tienen comentario (aquí, `main`), lo que sirve como lista de pendientes.

## Qué documentar

* **Sí:** las clases y funciones **públicas**: lo que usarán otras personas (o tú mismo dentro de un mes).
* **Sí:** las excepciones que pueden lanzar y las **condiciones** que deben cumplir los datos de entrada.
* **No:** lo evidente (`/// Devuelve el nombre.` sobre `getNombre()`). Una documentación vacía es ruido.
* **Mantenla al día:** una documentación que dice algo distinto de lo que hace el código es peor que no tenerla.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Comentar cada línea | Solo el porqué de lo que no sea obvio |
| Documentar con frases que repiten el nombre del método | Decir algo que el nombre no diga: unidades, límites, errores |
| Dejar código antiguo comentado | Borrarlo (Git guarda el historial) |
| Cambiar el código y olvidar actualizar el comentario | Repasa los comentarios cuando modifiques lo que describen |

## Para practicar

Haz los ejercicios de [U6.3 · Comentarios y documentación](documentacion.md): documentar un código, generar la documentación y limpiar comentarios que sobran. [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) también incluye cómo se escriben los comentarios en cada lenguaje.
