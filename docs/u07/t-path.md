# 7.E Ficheros con Path, bytes y bloques

En [7.B](t-archivos.md) y [7.C](t-texto.md) trabajaste con `File` y con métodos cortos como `Files.readAllLines`. Aquí se completa el cuadro con lo que suele pedirse en el módulo de Acceso a Datos:

* La clase **`Path`** y las operaciones de **`Files`**, con las **excepciones concretas** de cada una.
* Los **ficheros binarios**: se guardan **bytes**, no letras.
* **Leer y escribir por bloques**: en vez de cargar todo el archivo de golpe, se trabaja con un trozo cada vez.

Todos los ejemplos de esta página se han **ejecutado** con Java 25 en una carpeta vacía, en Windows (por eso las rutas llevan `\`).

## Path y Files: operaciones y errores

Un `Path` representa una **ruta** del disco. Se crea con `Path.of("…")` (no es un `String`) y las rutas se unen con `resolve`: `Path.of("datos").resolve("nota.txt")` es la ruta `datos\nota.txt`. Que exista un `Path` no significa que el archivo exista: es solo una ruta. Las operaciones están en la clase **`Files`**, todas **estáticas**.

| Quiero… | Se escribe |
|---|---|
| Nombre y carpeta padre | `ruta.getFileName()` · `ruta.getParent()` (puede ser `null`) |
| Ruta absoluta | `ruta.toAbsolutePath()` |
| Saber si existe | `Files.exists(ruta)` |
| Saber si es archivo o carpeta | `Files.isRegularFile(ruta)` · `Files.isDirectory(ruta)` |
| Tamaño en bytes | `Files.size(ruta)` (un `long`) |
| Crear una carpeta | `Files.createDirectory(ruta)`; con las que falten: `Files.createDirectories(ruta)` |
| Crear un archivo vacío | `Files.createFile(ruta)` |
| Copiar | `Files.copy(origen, destino)` |
| Mover o cambiar de nombre | `Files.move(origen, destino)` |
| Borrar (falla si no existe) | `Files.delete(ruta)` |
| Borrar si existe (devuelve `true` o `false`) | `Files.deleteIfExists(ruta)` |

!!! warning "Casi todo lanza `IOException`"
    Las operaciones de `Files` lanzan **`IOException`**, una excepción **comprobada**: el compilador te obliga a capturarla con `try`/`catch` o a declararla con `throws IOException` en el método. Si no, el programa **no compila**.

Un recorrido completo, con `try`, `catch` y un `finally` que limpia lo que se haya creado:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Recorrido {
    public static void main(String[] args) {
        Path carpeta = Path.of("datos");
        Path nota = carpeta.resolve("nota.txt");
        try {
            Files.createDirectories(carpeta);
            Files.createFile(nota);
            Files.writeString(nota, "hola");
            System.out.println("Ruta absoluta: " + nota.toAbsolutePath());
            System.out.println("Nombre: " + nota.getFileName() + " | Padre: " + nota.getParent());
            System.out.println("Es archivo: " + Files.isRegularFile(nota) + " | Es carpeta: " + Files.isDirectory(nota));
            System.out.println("Tamaño: " + Files.size(nota) + " bytes");
            Path copia = Files.copy(nota, carpeta.resolve("copia.txt"));
            Path movido = Files.move(copia, carpeta.resolve("final.txt"));
            System.out.println("Existe copia.txt: " + Files.exists(copia) + " | Existe final.txt: " + Files.exists(movido));
        } catch (IOException e) {
            System.out.println("Error: " + e.getClass().getSimpleName() + " - " + e.getMessage());
        } finally {
            try {
                Files.deleteIfExists(carpeta.resolve("final.txt"));
                Files.deleteIfExists(nota);
                Files.deleteIfExists(carpeta);
            } catch (IOException e) {
                System.out.println("No se pudo limpiar: " + e.getMessage());
            }
            System.out.println("Limpieza hecha: existe la carpeta = " + Files.exists(carpeta));
        }
    }
}
```

**Salida:**

```text
Ruta absoluta: C:\Users\ana\proyecto\datos\nota.txt
Nombre: nota.txt | Padre: datos
Es archivo: true | Es carpeta: false
Tamaño: 4 bytes
Existe copia.txt: false | Existe final.txt: true
Limpieza hecha: existe la carpeta = false
```

El `finally` se ejecuta **siempre**, haya habido error o no. Por eso es el sitio para borrar lo que el programa creó (y como borrar también puede lanzar `IOException`, lleva su propio `try`).

### Qué pasa cuando algo falla

Todas las excepciones de ficheros son subclases de **`IOException`**. Mira cuáles saltan y cuáles no:

```java
import java.nio.file.*;
import java.util.concurrent.Callable;

public class Fallos {
    static void intentar(String texto, Callable<Object> accion) {
        try {
            System.out.println(texto + " -> " + accion.call());
        } catch (Exception e) {
            System.out.println(texto + " -> " + e.getClass().getSimpleName());
        }
    }

    public static void main(String[] args) {
        Path carpeta = Path.of("datos");
        Path nota = carpeta.resolve("nota.txt");
        intentar("crear carpeta", () -> Files.createDirectory(carpeta));
        intentar("crear la carpeta otra vez", () -> Files.createDirectory(carpeta));
        intentar("createDirectories con la carpeta ya creada", () -> Files.createDirectories(carpeta));
        intentar("crear archivo", () -> Files.createFile(nota));
        intentar("crear el archivo otra vez", () -> Files.createFile(nota));
        intentar("tamaño de uno que no existe", () -> Files.size(carpeta.resolve("no.txt")));
        intentar("copiar", () -> Files.copy(nota, carpeta.resolve("copia.txt")));
        intentar("copiar sobre uno que existe", () -> Files.copy(nota, carpeta.resolve("copia.txt")));
        intentar("copiar con REPLACE_EXISTING", () -> Files.copy(nota, carpeta.resolve("copia.txt"), StandardCopyOption.REPLACE_EXISTING));
        intentar("copiar uno que no existe", () -> Files.copy(carpeta.resolve("no.txt"), carpeta.resolve("x.txt")));
        intentar("mover sobre uno que existe", () -> Files.move(nota, carpeta.resolve("copia.txt")));
        intentar("borrar uno que no existe", () -> { Files.delete(carpeta.resolve("no.txt")); return "ok"; });
        intentar("deleteIfExists de uno que no existe", () -> Files.deleteIfExists(carpeta.resolve("no.txt")));
        intentar("borrar una carpeta con cosas dentro", () -> { Files.delete(carpeta); return "ok"; });
        intentar("createDirectory sin la carpeta padre", () -> Files.createDirectory(carpeta.resolve("a").resolve("b")));
        intentar("createDirectories sin la carpeta padre", () -> Files.createDirectories(carpeta.resolve("a").resolve("b")));
        intentar("leer uno que no existe", () -> Files.readString(carpeta.resolve("no.txt")));
    }
}
```

**Salida:**

```text
crear carpeta -> datos
crear la carpeta otra vez -> FileAlreadyExistsException
createDirectories con la carpeta ya creada -> datos
crear archivo -> datos\nota.txt
crear el archivo otra vez -> FileAlreadyExistsException
tamaño de uno que no existe -> NoSuchFileException
copiar -> datos\copia.txt
copiar sobre uno que existe -> FileAlreadyExistsException
copiar con REPLACE_EXISTING -> datos\copia.txt
copiar uno que no existe -> NoSuchFileException
mover sobre uno que existe -> FileAlreadyExistsException
borrar uno que no existe -> NoSuchFileException
deleteIfExists de uno que no existe -> false
borrar una carpeta con cosas dentro -> DirectoryNotEmptyException
createDirectory sin la carpeta padre -> NoSuchFileException
createDirectories sin la carpeta padre -> datos\a\b
leer uno que no existe -> NoSuchFileException
```

| Excepción | Cuándo se produce |
|---|---|
| `FileAlreadyExistsException` | Crear, copiar o mover algo donde **ya hay** un archivo o una carpeta con ese nombre |
| `NoSuchFileException` | La ruta no existe: tamaño, copia, borrado, lectura... o `createDirectory` cuando falta la carpeta padre |
| `DirectoryNotEmptyException` | Borrar una carpeta que todavía tiene cosas dentro |
| `IOException` | La general: todas las anteriores son tipos de `IOException` |

Se capturan de la más concreta a la más general. Con `e.getFile()` (en las que son `FileSystemException`) o `e.getMessage()` se sabe qué ruta falló.

!!! info "Java no sobrescribe por su cuenta"
    A diferencia de Python o Dart, **`Files.copy` y `Files.move` no pisan un archivo que ya existe**: lanzan `FileAlreadyExistsException`. Para sobrescribir hay que pedirlo con `StandardCopyOption.REPLACE_EXISTING`. Y `Files.createDirectories` no falla si la carpeta ya existe.

## Ficheros binarios

En un fichero binario cada elemento es un **byte**. Se abre con `Files.newOutputStream(ruta)` (escribir) o `Files.newInputStream(ruta)` (leer), siempre dentro de **`try`-con-recursos** (`try (… )`), que cierra el archivo al terminar aunque haya un error.

* `salida.write(numero)` escribe **un** byte.
* `entrada.read()` lee un byte y lo devuelve como un `int` de 0 a 255. Cuando no queda nada, devuelve **`-1`**.

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class Bytes1 {
    public static void main(String[] args) throws IOException {
        Path fichero = Path.of("datos.bin");

        try (OutputStream salida = Files.newOutputStream(fichero)) {
            for (int numero : new int[]{72, 111, 108, 97}) {
                salida.write(numero);
            }
        }
        System.out.println("Tamaño: " + Files.size(fichero) + " bytes");

        try (InputStream entrada = Files.newInputStream(fichero)) {
            int dato = entrada.read();
            while (dato != -1) {
                System.out.println("Leído " + dato + " = " + (char) dato);
                dato = entrada.read();
            }
        }
    }
}
```

**Salida:**

```text
Tamaño: 4 bytes
Leído 72 = H
Leído 111 = o
Leído 108 = l
Leído 97 = a
```

Para archivos pequeños hay atajos: `Files.write(ruta, bytes)` y `Files.readAllBytes(ruta)`.

!!! warning "El tipo `byte` de Java va de −128 a 127"
    `read()` devuelve el byte como un `int` de 0 a 255, pero `Files.readAllBytes` devuelve `byte[]`, y un `byte` **con signo**: el 200 se ve como `-56`. Para recuperar el valor de 0 a 255, `byte & 0xFF`. Además, si a `write` le pasas un número mayor que 255, Java **se queda con los 8 bits de abajo** sin avisar: el 300 se guarda como `300 − 256 = 44`.

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class Bytes2 {
    public static void main(String[] args) throws IOException {
        Path fichero = Path.of("datos.bin");
        try (OutputStream salida = Files.newOutputStream(fichero)) {
            salida.write(200);
        }
        try (InputStream entrada = Files.newInputStream(fichero)) {
            System.out.println("read(): " + entrada.read());
        }
        byte[] bytes = Files.readAllBytes(fichero);
        System.out.println("readAllBytes()[0]: " + bytes[0]);
        System.out.println("readAllBytes()[0] como 0..255: " + (bytes[0] & 0xFF));

        try (OutputStream salida = Files.newOutputStream(fichero)) {
            salida.write(300);          // no cabe en un byte
        }
        System.out.println("Después de escribir 300: " + (Files.readAllBytes(fichero)[0] & 0xFF));
    }
}
```

**Salida:**

```text
read(): 200
readAllBytes()[0]: -56
readAllBytes()[0] como 0..255: 200
Después de escribir 300: 44
```

### Leer y escribir por bloques

Leer byte a byte es lento con archivos grandes. Lo habitual es usar un **buffer** (un `byte[]`) y leer un bloque de golpe:

* `entrada.read(buffer)` rellena el buffer y **devuelve cuántos bytes ha leído** (o `-1` si ya no queda nada).
* `salida.write(buffer, 0, leidos)` escribe **solo** los `leidos` primeros bytes del buffer.

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class Bloques {
    public static void main(String[] args) throws IOException {
        Path origen = Path.of("origen.bin");
        byte[] prueba = new byte[25];
        for (int i = 0; i < prueba.length; i++) prueba[i] = (byte) (65 + i);     // 25 bytes de prueba
        Files.write(origen, prueba);

        Path copia = Path.of("copia.bin");
        try (InputStream entrada = Files.newInputStream(origen);
             OutputStream salida = Files.newOutputStream(copia)) {
            byte[] buffer = new byte[10];
            int leidos = entrada.read(buffer);
            while (leidos != -1) {
                System.out.println("Bloque de " + leidos + " bytes");
                salida.write(buffer, 0, leidos);
                leidos = entrada.read(buffer);
            }
        }
        System.out.println("Origen: " + Files.size(origen) + " bytes | Copia: " + Files.size(copia) + " bytes");
    }
}
```

**Salida:**

```text
Bloque de 10 bytes
Bloque de 10 bytes
Bloque de 5 bytes
Origen: 25 bytes | Copia: 25 bytes
```

El último bloque trae solo 5 bytes, aunque el buffer sea de 10. Si se escribe el buffer entero, se copia basura:

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class BloquesMal {
    public static void main(String[] args) throws IOException {
        Path origen = Path.of("origen.bin");
        byte[] prueba = new byte[25];
        for (int i = 0; i < prueba.length; i++) prueba[i] = (byte) (65 + i);
        Files.write(origen, prueba);

        Path mala = Path.of("mala.bin");
        try (InputStream entrada = Files.newInputStream(origen);
             OutputStream salida = Files.newOutputStream(mala)) {
            byte[] buffer = new byte[10];
            int leidos = entrada.read(buffer);
            while (leidos != -1) {
                salida.write(buffer);           // ¡escribe los 10 bytes aunque se hayan leído menos!
                leidos = entrada.read(buffer);
            }
        }
        System.out.println("Origen: " + Files.size(origen) + " bytes | Copia mal hecha: " + Files.size(mala) + " bytes");
    }
}
```

**Salida:**

```text
Origen: 25 bytes | Copia mal hecha: 30 bytes
```

!!! warning "Escribe solo lo que has leído"
    `salida.write(buffer)` escribe **todo** el buffer. En el último bloque conserva los bytes del bloque anterior, así que la copia sale más grande y estropeada. Usa siempre `write(buffer, 0, leidos)`.

## Ficheros de texto: caracteres, bytes y bloques

Un archivo de texto también son bytes. Para leerlo como letras hay que decir cómo se codifican, y por eso **caracteres y bytes no coinciden**: en UTF-8 las letras sin tilde ocupan 1 byte, pero la `ñ`, las vocales con tilde o el `€` ocupan 2 o 3. Además, `length()` en Java cuenta unidades de 16 bits (UTF-16), no «letras»: un emoji cuenta como 2.

```java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class CaracteresBytes {
    public static void main(String[] args) throws IOException {
        String texto = "Año 2026: 5 € ñandú\nsegunda línea\n";
        Path fichero = Path.of("texto.txt");
        Files.writeString(fichero, texto, StandardCharsets.UTF_8);

        System.out.println("length(): " + texto.length());
        System.out.println("codePointCount: " + texto.codePointCount(0, texto.length()));
        System.out.println("Bytes en UTF-8: " + texto.getBytes(StandardCharsets.UTF_8).length);
        System.out.println("Bytes en el disco: " + Files.size(fichero));

        String carita = "a😀";
        System.out.println("\"a😀\": length " + carita.length() + ", codePointCount " + carita.codePointCount(0, carita.length())
                + ", bytes " + carita.getBytes(StandardCharsets.UTF_8).length);
    }
}
```

**Salida:**

```text
length(): 34
codePointCount: 34
Bytes en UTF-8: 40
Bytes en el disco: 40
"a😀": length 3, codePointCount 2, bytes 5
```

`codePointCount` cuenta los caracteres Unicode de verdad. Para saber cuánto ocupa en el disco hay que mirar los bytes en UTF-8 o `Files.size`.

!!! tip "Indica siempre el juego de caracteres"
    Pasa **`StandardCharsets.UTF_8`** al abrir o escribir texto (`Files.newBufferedReader(ruta, StandardCharsets.UTF_8)`, `Files.writeString(ruta, texto, StandardCharsets.UTF_8)`). Así el programa se comporta igual en cualquier versión de Java y en cualquier sistema.

Para leer y escribir **caracteres** se usan `Reader` y `Writer`, con un buffer `char[]`; el patrón es el mismo que con los bytes:

* `lector.read(buffer)` devuelve cuántos caracteres ha leído, o `-1`.
* `new String(buffer, 0, leidos)` convierte en texto solo la parte leída.

```java
import java.io.IOException;
import java.io.Reader;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class TextoBloques {
    public static void main(String[] args) throws IOException {
        Path fichero = Path.of("texto.txt");
        Files.writeString(fichero, "Año 2026: 5 € ñandú\nsegunda línea\n", StandardCharsets.UTF_8);

        try (Reader lector = Files.newBufferedReader(fichero, StandardCharsets.UTF_8)) {
            char[] buffer = new char[8];
            int leidos = lector.read(buffer);
            int n = 1;
            while (leidos != -1) {
                String trozo = new String(buffer, 0, leidos).replace("\n", "|");
                System.out.println("Bloque " + n + " (" + leidos + " caracteres): [" + trozo + "]");
                leidos = lector.read(buffer);
                n++;
            }
        }
    }
}
```

**Salida:**

```text
Bloque 1 (8 caracteres): [Año 2026]
Bloque 2 (8 caracteres): [: 5 € ña]
Bloque 3 (8 caracteres): [ndú|segu]
Bloque 4 (8 caracteres): [nda líne]
Bloque 5 (2 caracteres): [a|]
```

El error clásico es el mismo que con los bytes: usar todo el buffer en el último bloque.

```java
import java.io.IOException;
import java.io.Reader;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class TextoMal {
    public static void main(String[] args) throws IOException {
        Path fichero = Path.of("texto.txt");
        Files.writeString(fichero, "Año 2026: 5 € ñandú\nsegunda línea\n", StandardCharsets.UTF_8);

        try (Reader lector = Files.newBufferedReader(fichero, StandardCharsets.UTF_8)) {
            char[] buffer = new char[8];
            int leidos = lector.read(buffer);
            String ultimo = "";
            while (leidos != -1) {
                ultimo = new String(buffer).replace("\n", "|");      // ¡usa el buffer entero!
                leidos = lector.read(buffer);
            }
            System.out.println("Último bloque: [" + ultimo + "]");
        }
    }
}
```

**Salida:**

```text
Último bloque: [a|a líne]
```

El último bloque tenía 2 caracteres (`a` y el salto de línea) y el resto del buffer conservaba lo del bloque anterior. Para escribir por bloques desde un `char[]` se usa `escritor.write(caracteres, inicio, longitud)`:

```java
import java.io.IOException;
import java.io.Writer;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class EscribirBloques {
    public static void main(String[] args) throws IOException {
        char[] letras = "ABCDEFGHIJKLMNOPQRSTU".toCharArray();      // 21 caracteres
        Path destino = Path.of("letras.txt");
        int escrituras = 0;
        try (Writer escritor = Files.newBufferedWriter(destino, StandardCharsets.UTF_8)) {
            for (int inicio = 0; inicio < letras.length; inicio += 8) {
                int cuantos = Math.min(8, letras.length - inicio);   // el último bloque es más corto
                escritor.write(letras, inicio, cuantos);
                escrituras++;
            }
        }
        System.out.println("Escrituras: " + escrituras);
        System.out.println("Contenido: " + Files.readString(destino, StandardCharsets.UTF_8));
    }
}
```

**Salida:**

```text
Escrituras: 3
Contenido: ABCDEFGHIJKLMNOPQRSTU
```

!!! note "Y si solo quieres las líneas"
    Para recorrer un archivo línea a línea no hace falta un buffer: `Files.readAllLines(ruta)` o `Files.lines(ruta)` (ver [7.C](t-texto.md)). Los bloques se usan cuando el archivo es grande o cuando te piden leer un número fijo de caracteres.

## Errores frecuentes

* **Olvidar el `try`-con-recursos**: el archivo se queda abierto y puede no guardarse todo lo escrito.
* **No comprobar el `-1`**: sin él, el bucle de lectura no termina o procesa datos que no existen.
* **Escribir el buffer entero** en vez de `write(buffer, 0, leidos)` (o `new String(buffer)` en vez de `new String(buffer, 0, leidos)`).
* **Creer que `byte` va de 0 a 255**: va de −128 a 127; usa `& 0xFF`.
* **Esperar que `copy` o `move` sobrescriban**: lanzan `FileAlreadyExistsException` salvo que indiques `REPLACE_EXISTING`.
* **Tratar un `Path` como un `String`**: se crea con `Path.of("…")` y se une con `resolve`.
* **Olvidar `throws IOException`** o el `catch`: el programa no compila.

## Para practicar

Los ejercicios [7.16 a 7.21](path.md) usan todo lo de esta página.
