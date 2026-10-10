# 7.C Ficheros de texto

Un **fichero de texto** guarda caracteres legibles (notas, configuraciones, registros, CSV, JSON...). Se puede abrir con cualquier editor, a diferencia de un fichero **binario** (una imagen, un ejecutable). Leer y escribir texto es lo más habitual al trabajar con datos.

!!! tip "Leer y escribir por bloques"
    Aquí se lee y escribe el archivo entero o línea a línea. Para trabajar **por bloques de caracteres** (`char[]`) y con ficheros **binarios** (bytes) mira [7.E](t-path.md).

## Crear, añadir y leer

`Files.writeString` **crea o sobrescribe**; para **añadir** se pasa `StandardOpenOption.APPEND`. `Files.readAllLines` devuelve la lista de líneas y `Files.readString`, el contenido completo. Para leer o escribir **línea a línea** sin cargar todo en memoria se usa un `BufferedReader` y un `BufferedWriter` (`Files.newBufferedReader`/`newBufferedWriter`) dentro de un **`try-with-resources`**, que **cierra** los archivos al terminar aunque haya un error. La codificación por defecto en las funciones de `Files` es UTF-8.

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.NoSuchFileException;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.util.List;

public class Txt1 {
    public static void main(String[] args) throws IOException {
        Path archivo = Path.of("texto.txt");
        Files.writeString(archivo, "primera línea\nsegunda línea\n"); // crea o sobrescribe
        Files.writeString(archivo, "tercera\n", StandardOpenOption.APPEND); // añade al final

        List<String> lineas = Files.readAllLines(archivo);
        System.out.println(lineas.size() + " líneas");
        int palabras = 0;
        for (int i = 0; i < lineas.size(); i++) {
            System.out.println((i + 1) + ": " + lineas.get(i));
            palabras += lineas.get(i).split(" ").length;
        }
        System.out.println("palabras: " + palabras);

        try {
            Files.readString(Path.of("falta.txt"));
        } catch (NoSuchFileException e) {
            System.out.println("No se pudo abrir 'falta.txt': el archivo no existe");
        }

        Path copia = Path.of("copia.txt");
        try (BufferedReader entrada = Files.newBufferedReader(archivo);
             BufferedWriter salida = Files.newBufferedWriter(copia)) {
            String linea;
            while ((linea = entrada.readLine()) != null) {
                salida.write(linea.toUpperCase());
                salida.newLine();
            }
        }
        System.out.println("primera línea en mayúsculas: " + Files.readAllLines(copia).get(0));

        Files.delete(archivo);
        Files.delete(copia);
    }
}
```

Salida:

```text
3 líneas
1: primera línea
2: segunda línea
3: tercera
palabras: 5
No se pudo abrir 'falta.txt': el archivo no existe
primera línea en mayúsculas: PRIMERA LÍNEA
```

Qué hace el programa, paso a paso:

1. **Escribe** dos líneas y después **añade** una tercera (sin borrar las anteriores).
2. **Lee** el archivo completo como lista de líneas y las muestra numeradas, contando las palabras.
3. **Intenta abrir un archivo que no existe** y avisa con un mensaje claro en lugar de dejar que el programa se detenga. Abrir un archivo que no existe lanza una **`NoSuchFileException`** (subclase de `IOException`).
4. **Copia** el archivo en mayúsculas leyendo **línea a línea**, y comprueba el resultado.
5. **Borra** los archivos de prueba.

## Tres decisiones al abrir un archivo

| Decisión | Opciones |
|---|---|
| **¿Qué hago con lo que ya hay?** | **Sobrescribir** (el contenido anterior se pierde) o **añadir** al final |
| **¿Cómo lo leo?** | **Entero** de una vez (cómodo, solo para ficheros pequeños) o **línea a línea** (cualquier tamaño, gasta poca memoria) |
| **¿Con qué codificación?** | **UTF-8**, que sirve para tildes, eñes y símbolos. Si el archivo se guardó con otra, las tildes salen mal |

!!! warning "Escribir borra"
    La operación de «escribir» **vacía** el archivo si ya existía. Si no quieres perder su contenido, **añade** en lugar de escribir, o pregunta antes de sobrescribir (como piden los ejercicios).

## Cerrar siempre el archivo

Un archivo abierto ocupa recursos del sistema y puede quedar **a medias** (con datos aún sin escribir en el disco) si no se cierra. Por eso se abre dentro de una estructura que lo **cierra automáticamente**, incluso si hay un error en medio. En Java: `try-with-resources`.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Perder el contenido anterior al escribir | Añadir (`append`) o preguntar antes de sobrescribir |
| Cargar en memoria un archivo enorme | Leer línea a línea |
| Tildes que se ven mal (`Ã¡` en vez de `á`) | Indicar UTF-8 al abrir (en Python, siempre `encoding`) |
| No cerrar el archivo | `with` / `use` / `try-with-resources` |
| Dar por hecho que el archivo existe | Capturar el error o comprobar antes |
| Contar las líneas sin tener en cuenta la última línea vacía | Probar con archivos que terminen y no terminen en salto de línea |

## Para practicar

Haz los ejercicios de [U7.3 · Ficheros de texto](texto.md). [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) muestra cómo se escribe lo mismo en otro lenguaje.
