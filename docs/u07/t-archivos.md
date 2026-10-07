# 7.B Archivos y carpetas

Los programas guardan datos en **archivos**, organizados en **carpetas** (directorios). Saber consultar, crear, copiar, mover y borrar es la base para cualquier programa que trabaje con datos que deben sobrevivir al cierre.

## Rutas

Una **ruta** indica dónde está un archivo o carpeta.

| Tipo | Ejemplo | Significa |
|---|---|---|
| **Absoluta** | `C:/Users/ana/datos/notas.txt` (Windows) · `/home/ana/datos/notas.txt` (Linux, macOS) | Desde la raíz del sistema |
| **Relativa** | `datos/notas.txt` | Desde la **carpeta de trabajo** del programa (la carpeta desde la que se ejecuta) |

!!! tip "Usa `/` en las rutas"
    Aunque Windows escribe las rutas con `\`, en Java (y en los otros tres lenguajes) la barra normal `/` funciona en todos los sistemas y evita problemas con el carácter de escape `\`.

## Las operaciones básicas

Se usa el paquete **`java.nio.file`**: **`Path`** representa una ruta y **`Files`** reúne todas las operaciones (`exists`, `isDirectory`, `size`, `copy`, `move`, `delete`, `list`...). Existe también la clase antigua `java.io.File`, que sigue funcionando pero es menos completa; para código nuevo se recomienda `Path` y `Files`. Las operaciones lanzan `IOException` (una excepción **comprobada**), así que hay que capturarla o declararla con `throws`.

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Stream;

public class Arch1 {
    static List<String> nombres(Path carpeta) throws IOException {
        List<String> lista = new ArrayList<>();
        try (Stream<Path> hijos = Files.list(carpeta)) {
            hijos.forEach(p -> lista.add(p.getFileName().toString()));
        }
        Collections.sort(lista);
        return lista;
    }

    static void borrarTodo(Path raiz) throws IOException {
        try (Stream<Path> todo = Files.walk(raiz)) {
            for (Path p : todo.sorted(Comparator.reverseOrder()).toList()) {
                Files.delete(p);
            }
        }
    }

    public static void main(String[] args) throws IOException {
        Path carpeta = Path.of("datos");
        Files.createDirectories(carpeta.resolve("sub"));
        System.out.println("carpeta creada: " + (Files.exists(carpeta) ? "sí" : "no"));

        Files.writeString(carpeta.resolve("notas.txt"), "uno\ndos\n");
        for (String nombre : nombres(carpeta)) {
            Path ruta = carpeta.resolve(nombre);
            if (Files.isDirectory(ruta)) {
                System.out.println(nombre + ": carpeta");
            } else {
                System.out.println(nombre + ": archivo de " + Files.size(ruta) + " bytes");
            }
        }

        Files.copy(carpeta.resolve("notas.txt"), carpeta.resolve("notas_copia.txt"));
        System.out.println("tras copiar: " + String.join(", ", nombres(carpeta)));
        Files.move(carpeta.resolve("notas_copia.txt"), carpeta.resolve("resumen.txt"));
        System.out.println("tras renombrar: " + String.join(", ", nombres(carpeta)));

        borrarTodo(carpeta);
        System.out.println("datos existe tras borrar: " + (Files.exists(carpeta) ? "sí" : "no"));
    }
}
```

Salida:

```text
carpeta creada: sí
notas.txt: archivo de 8 bytes
sub: carpeta
tras copiar: notas.txt, notas_copia.txt, sub
tras renombrar: notas.txt, resumen.txt, sub
datos existe tras borrar: no
```

El programa crea una carpeta con una subcarpeta, escribe un archivo, **inspecciona** lo que hay (¿archivo o carpeta? ¿cuánto ocupa?), lo copia, lo renombra y lo borra todo al final. Fíjate en que la lista de nombres se **ordena** antes de mostrarla: el sistema no garantiza ningún orden al listar una carpeta.

| Necesito... | En Java |
|---|---|
| ¿Existe? ¿Es carpeta? | `Files.exists(p)` · `Files.isDirectory(p)` |
| Tamaño en bytes | `Files.size(p)` |
| Crear carpeta (con las que falten) | `Files.createDirectories(p)` |
| Listar el contenido | `Files.list(p)` (hay que **cerrarlo**: `try-with-resources`) |
| Copiar · renombrar o mover | `Files.copy(a, b)` · `Files.move(a, b)` |
| Borrar archivo · carpeta con todo lo que contiene | `Files.delete(p)` (solo si está vacía) · recorrer con `Files.walk` y borrar de dentro hacia fuera |

## Cuando algo falla

Los errores son subclases de **`IOException`**: `NoSuchFileException` (no existe), `FileAlreadyExistsException`, `AccessDeniedException` (sin permisos), `DirectoryNotEmptyException` (borrar una carpeta con contenido).

Los fallos son **normales** con archivos (el usuario escribe mal una ruta, el disco está lleno, otro programa tiene el archivo abierto). Un programa robusto **comprueba antes** lo que pueda (¿existe?) y **captura** lo demás para dar un mensaje claro.

!!! danger "Cuidado con lo que borras"
    Borrar con `recursive`, `rmtree`, `deleteRecursively` o `walk` elimina **todo** lo que haya dentro, sin papelera y sin vuelta atrás. Antes de borrar:

    * Comprueba que la ruta es **la que esperas** (no vacía, no la raíz de un disco, no tu carpeta de usuario).
    * Si el borrado lo decide el usuario, **pídele confirmación**.
    * Mientras pruebas, usa una carpeta de prueba aparte.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Rutas relativas que dependen de dónde se ejecute el programa | Comprobar la carpeta de trabajo, o usar rutas absolutas |
| Asumir un orden al listar una carpeta | Ordenar los nombres |
| Sobrescribir un archivo existente sin avisar | Comprobar si existe y preguntar antes |
| Borrar una carpeta no vacía con la función de un solo archivo | Usar la versión recursiva, con confirmación |
| Olvidar cerrar lo que se abre (en Java, `Files.list`) | `try-with-resources` |

## Para practicar

Haz los ejercicios de [U7.2 · Archivos y carpetas](archivos.md). Y para ver cómo se escribe en otro lenguaje: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
