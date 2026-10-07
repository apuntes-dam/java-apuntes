# 7.A Consola: entrada y salida estándar

Cuando un programa se ejecuta en una terminal, el sistema le da **tres canales** de texto, llamados **flujos estándar**:

| Flujo | Nombre | Para qué sirve | Por defecto |
|---|---|---|---|
| `stdin` | Entrada estándar | Lo que el programa **recibe** | El teclado |
| `stdout` | Salida estándar | Los **resultados** normales | La pantalla |
| `stderr` | Salida de error | Los **avisos y errores** | La pantalla |

Los dos de salida van a la pantalla, pero son **canales distintos**, y esa diferencia permite separar los resultados de los mensajes de error.

## Mostrar datos con formato

```java
import java.util.List;
import java.util.Locale;

public class Cons1 {
    record Equipo(String nombre, int puntos, int diferencia) {}

    public static void main(String[] args) {
        List<Equipo> equipos = List.of(new Equipo("Águilas", 45, 12), new Equipo("Lobos", 41, 5), new Equipo("Tigres", 38, -2));

        System.out.println(String.format("%-12s%5s%6s", "Equipo", "Pts", "Dif"));
        System.out.println("-".repeat(23));
        int total = 0;
        for (Equipo e : equipos) {
            System.out.println(String.format("%-12s%5d%+6d", e.nombre(), e.puntos(), e.diferencia()));
            total += e.puntos();
        }
        System.out.println("Media de puntos: " + String.format(Locale.ROOT, "%.2f", (double) total / equipos.size()));

        System.err.println("Aviso: datos de ejemplo, no oficiales");
        System.out.println("Fin del informe");
    }
}
```

Salida:

```text
Equipo        Pts   Dif
-----------------------
Águilas        45   +12
Lobos          41    +5
Tigres         38    -2
Media de puntos: 41.33
Fin del informe
```

Qué conviene aprender de este ejemplo:

* **Columnas alineadas.** Se reserva un **ancho fijo** para cada columna: el texto a la izquierda y los números a la derecha. Sin eso, las columnas quedan descuadradas.
* **Decimales fijos.** La media sale con dos decimales aunque el cálculo dé muchos más.
* **El aviso va por otro canal.** Verás que la línea «Aviso: datos de ejemplo, no oficiales» **no** aparece en el recuadro de arriba: se escribe en `stderr`, no en `stdout`. En una terminal sí se vería.

| Necesito... | En Java |
|---|---|
| Alinear a la izquierda | `%-12s` |
| Alinear a la derecha | `%5d` |
| Decimales fijos | `%.2f` |
| Repetir un texto | `"-".repeat(23)` |
| Mostrar sin salto de línea | `System.out.print("texto")` |
| Con signo | `%+d` |

`String.format` usa la **configuración regional del equipo**: en un Windows en español, `%.2f` escribe `41,33` (con coma). Para que siempre salga con punto, pasa `Locale.ROOT` como primer argumento (`String.format(Locale.ROOT, "%.2f", x)`), como hace el ejemplo.

## Salida estándar y salida de error

Separarlas sirve para que un programa pueda **guardar los resultados en un archivo sin mezclarlos con los errores**. En una terminal, el operador `>` redirige la salida normal y `2>` la de error:

```bash
programa > resultados.txt 2> errores.txt
```

Con el ejemplo anterior, `resultados.txt` contendría la tabla y la media y `errores.txt` solo el aviso:

```bash
java Cons1.java > resultados.txt 2> errores.txt
```

Para **dar entrada desde un archivo** en lugar del teclado se usa `<` (`programa < entrada.txt`), y para encadenar programas, la **tubería** `|`.

## Leer datos

Se lee con un **`Scanner`** sobre `System.in`: `nextLine()` lee una línea completa y `hasNextLine()` dice si queda alguna. Un fallo clásico: **`nextInt()` no consume el salto de línea**, así que un `nextLine()` posterior devuelve una cadena vacía. Lo más seguro es leer siempre con `nextLine()` y convertir después con `Integer.parseInt`. La salida de error es `System.err`.

```java
import java.util.Scanner;

public class Cons2 {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.println("Escribe líneas (una vacía para terminar):");
        int lineas = 0;
        int palabras = 0;
        while (teclado.hasNextLine()) {
            String linea = teclado.nextLine();
            if (linea.isEmpty()) {
                break;
            }
            lineas++;
            palabras += linea.trim().split("\\s+").length;
        }
        System.out.println("Líneas: " + lineas);
        System.out.println("Palabras: " + palabras);
    }
}
```

Este programa lee líneas **hasta encontrar una vacía** (o hasta que se acabe la entrada). Si se le escribe `hola mundo`, `esto es una prueba` y una línea vacía, muestra:

```text
Escribe líneas (una vacía para terminar):
Líneas: 2
Palabras: 6
```

Una entrada del usuario es **siempre texto**: si necesitas un número hay que convertirlo y **prever que falle** (ver el patrón de «pedir hasta que sea válido» en [2.3 Excepciones](../u02/02-excepciones.md)).

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Columnas descuadradas | Fijar un ancho para cada columna y alinear números a la derecha |
| Mezclar resultados y errores en la misma salida | Escribir los avisos en `stderr` |
| Dar por hecho que siempre hay una línea más que leer | Comprobar el fin de la entrada (`null`, `hasNextLine`, `EOFError`) |
| Usar el número leído sin convertirlo ni validarlo | Convertir dentro de un `try` y volver a pedir |
| Decimales con coma en unas máquinas y con punto en otras | Fijar la configuración regional al formatear (en Java y Kotlin) |

## Para practicar

Haz los ejercicios de [U7.1 · Consola](consola.md). Para ver cómo se escribe en otro lenguaje: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
