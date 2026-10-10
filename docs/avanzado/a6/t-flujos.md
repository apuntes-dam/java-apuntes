# A6.B Hablar con el proceso hijo

Un hijo tiene tres flujos: **entrada** (`stdin`), **salida** (`stdout`) y **salida de error** (`stderr`). Desde el punto de vista del **padre**, la dirección es la contraria a la que parece:

| Flujo del hijo | El padre usa | Dirección |
|---|---|---|
| `stdin` (lo que el hijo lee) | `getOutputStream()` o `outputWriter()` | **padre → hijo** |
| `stdout` (lo que el hijo escribe) | `getInputStream()` o `inputReader()` | **hijo → padre** |
| `stderr` (los errores del hijo) | `getErrorStream()` o `errorReader()` | **hijo → padre** |

!!! warning "El nombre confunde"
    **`getInputStream()` lee la *salida* del hijo**: «input» es entrada **para el padre**. Y `getOutputStream()` sirve para **enviar datos** al hijo.

## Leer lo que escribe el hijo

Lo habitual es leer **línea a línea**. Con `redirectErrorStream(true)` la salida de error se **mezcla** con la normal, y basta con leer un solo flujo:

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Path;

public class LeerLineas {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        ProcessBuilder pb = new ProcessBuilder(java, "Charlatan.java");
        pb.redirectErrorStream(true);                 // mezcla la salida de error con la salida normal
        Process p = pb.start();
        try (BufferedReader lector = p.inputReader(StandardCharsets.UTF_8)) {
            String linea;
            while ((linea = lector.readLine()) != null) {
                System.out.println("[hijo] " + linea);
            }
        }
        System.out.println("Código: " + p.waitFor());
    }
}
```

**Salida:**

```text
[hijo] linea 1 (salida normal)
[hijo] ERROR: algo ha ido mal
[hijo] linea 2 (salida normal)
[hijo] ERROR: otro fallo
[hijo] linea 3 (salida normal)
Código: 0
```

Si prefieres **separarlas**, se puede mandar una de ellas a un fichero con `redirectError(...)`:

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;

public class Separados {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        ProcessBuilder pb = new ProcessBuilder(java, "Charlatan.java");
        pb.redirectError(new File("errores.log"));    // la salida de error va a un fichero
        Process p = pb.start();
        try (BufferedReader lector = p.inputReader(StandardCharsets.UTF_8)) {
            String linea;
            while ((linea = lector.readLine()) != null) {
                System.out.println("normal: " + linea);
            }
        }
        p.waitFor();
        for (String linea : Files.readAllLines(Path.of("errores.log"))) {
            System.out.println("error:  " + linea);
        }
    }
}
```

**Salida:**

```text
normal: linea 1 (salida normal)
normal: linea 2 (salida normal)
normal: linea 3 (salida normal)
error:  ERROR: algo ha ido mal
error:  ERROR: otro fallo
```

## Enviar datos al hijo

Para darle datos al hijo se escribe en su `stdin`. Hay que **cerrar** el flujo cuando se terminan los datos: el hijo `Eco` sigue leyendo hasta que **no hay más**, y eso solo ocurre al cerrar.

```java
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Path;

public class Enviar {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        Process p = new ProcessBuilder(java, "Eco.java").start();
        try (BufferedWriter escritor = p.outputWriter(StandardCharsets.UTF_8)) {
            escritor.write("pera\nmanzana\nkiwi\n");
        }                                             // cerrar el flujo = «ya no hay más datos»
        System.out.print(new String(p.getInputStream().readAllBytes(), StandardCharsets.UTF_8));
        p.waitFor();
    }
}
```

**Salida:**

```text
ECO: PERA
ECO: MANZANA
ECO: KIWI
(fin de los datos)
```

!!! warning "Si no se cierra el flujo, el hijo espera para siempre"
    Aquí se envía un dato pero **no se cierra** el flujo. El hijo no sabe que no habrá más y se queda esperando; el padre lo comprueba con un límite de tiempo y lo detiene:

```java
import java.io.BufferedWriter;
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Path;
import java.util.concurrent.TimeUnit;

public class SinCerrar {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        Process p = new ProcessBuilder(java, "Eco.java").start();
        BufferedWriter escritor = p.outputWriter(StandardCharsets.UTF_8);
        escritor.write("pera\n");
        escritor.flush();                             // ¡se envía, pero NO se cierra el flujo!
        boolean terminado = p.waitFor(3, TimeUnit.SECONDS);
        System.out.println("¿Ha terminado el hijo? " + terminado);
        p.destroy();
    }
}
```

**Salida:**

```text
¿Ha terminado el hijo? false
```

## Redirecciones

Además de leer los flujos a mano, `ProcessBuilder` puede **conectarlos directamente** a ficheros o a la consola:

| Orden | Qué hace |
|---|---|
| `inheritIO()` | El hijo usa la consola del padre |
| `redirectOutput(File)` | La salida va a un fichero y lo **sobrescribe** |
| `redirectOutput(Redirect.appendTo(File))` | La salida **se añade** al final del fichero |
| `redirectError(...)`, `redirectInput(File)` | Lo mismo para la salida de error y para la entrada |
| `redirectErrorStream(true)` | Mezcla la salida de error con la normal |
| `Redirect.DISCARD` | Descarta la salida |

En este ejemplo el hijo se lanza **dos veces**: la salida normal se sobrescribe (queda con las líneas de **una** ejecución) y los errores se añaden (**dos** ejecuciones):

```java
import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;

public class Redirecciones {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        for (int vez = 1; vez <= 2; vez++) {
            ProcessBuilder pb = new ProcessBuilder(java, "Charlatan.java");
            pb.redirectOutput(new File("salida.log"));                          // SOBRESCRIBE
            pb.redirectError(ProcessBuilder.Redirect.appendTo(new File("errores.log")));   // AÑADE al final
            pb.start().waitFor();
        }
        List<String> salida = Files.readAllLines(Path.of("salida.log"));
        List<String> errores = Files.readAllLines(Path.of("errores.log"));
        System.out.println("salida.log:  " + salida.size() + " líneas");
        System.out.println("errores.log: " + errores.size() + " líneas");
    }
}
```

**Salida:**

```text
salida.log:  3 líneas
errores.log: 4 líneas
```

## Dos trampas clásicas

**1. Si no lees la salida del hijo, puede quedarse bloqueado.** Las tuberías tienen un **búfer** pequeño: cuando se llena, el hijo **se detiene** hasta que alguien lea. Aquí el hijo escribe cien mil líneas y el padre no lee ninguna:

```java
import java.io.IOException;
import java.nio.file.Path;
import java.util.concurrent.TimeUnit;

public class BufferLleno {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        Process p = new ProcessBuilder(java, "Parlanchin.java").start();     // escribe 100 000 líneas
        // ... y el padre NO lee su salida
        boolean terminado = p.waitFor(4, TimeUnit.SECONDS);
        System.out.println("¿Ha terminado el hijo? " + terminado);
        p.destroyForcibly();
    }
}
```

**Salida:**

```text
¿Ha terminado el hijo? false
```

Por eso: **lee la salida antes de llamar a `waitFor()`** (o mándala a un fichero con `redirectOutput`).

**2. Nunca pegues datos del usuario a una orden de la shell.** Si el padre construye la orden con `"echo " + dato`, quien escriba `hola & echo INYECTADO` consigue **ejecutar otra orden**. Es la **inyección de órdenes**. Lo seguro es que el dato viaje como **un argumento** de un programa, sin pasar por la shell:

```java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Path;

public class Inyeccion {
    static String ejecutar(ProcessBuilder pb) throws IOException, InterruptedException {
        pb.redirectErrorStream(true);
        Process p = pb.start();
        String salida = new String(p.getInputStream().readAllBytes(), StandardCharsets.UTF_8).trim();
        p.waitFor();
        return salida;
    }

    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        String dato = "hola & echo INYECTADO";         // lo que escribe el usuario

        System.out.println("--- MAL: el dato se pega a una orden de la shell (cmd /c) ---");
        System.out.println(ejecutar(new ProcessBuilder("cmd", "/c", "echo " + dato)));

        System.out.println("--- BIEN: el dato viaja como UN argumento de un programa ---");
        System.out.println(ejecutar(new ProcessBuilder(java, "Contexto.java", dato)));
    }
}
```

**Salida:**

```text
--- MAL: el dato se pega a una orden de la shell (cmd /c) ---
hola 
INYECTADO
--- BIEN: el dato viaja como UN argumento de un programa ---
MODO = null
Carpeta de trabajo: C:\Users\ana\proyecto
Argumentos: hola & echo INYECTADO
```

La primera forma ejecuta **dos órdenes** (la segunda no la ha escrito el programador). En la segunda, `&` es solo texto. La shell (`cmd /c`, `bash -c`) solo se usa cuando de verdad se necesitan tuberías, comodines u órdenes internas, y **nunca con datos sin controlar**.

## Qué recordar

* `getInputStream()` lee la **salida** del hijo; `getOutputStream()` le **envía** datos.
* Para enviar datos hay que **cerrar** el flujo al terminar.
* **Lee antes de `waitFor()`**: si no, el hijo puede bloquearse.
* `redirectOutput` sobrescribe; `Redirect.appendTo` añade.
* Datos del usuario: siempre como **argumentos**, nunca pegados a una orden de shell.

Siguiente: [A6.C Controlar y observar procesos](t-controlar.md).
