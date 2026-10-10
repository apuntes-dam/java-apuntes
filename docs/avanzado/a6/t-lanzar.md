# A6.A Lanzar un proceso

En la [web de Procesos y planificación de la CPU](https://apuntes-dam.github.io/procesos-apuntes/) se explica **qué es un proceso**: un programa en ejecución con su PID y su estado. Aquí se ve cómo **crear y controlar procesos desde el propio código de Java**: tu programa (el **padre**) lanza otro programa (el **hijo**), espera a que termine y mira cómo ha ido.

!!! info "Qué se ha ejecutado"
    Todos los ejemplos de esta unidad se han **ejecutado de verdad** con Java 25 en **Windows**. Como los hijos son programas de Java, el padre los lanza con `java Hijo.java`, que **arranca otra JVM** sin tener que compilar a mano. Los **PID** que verás serán otros en tu equipo, y donde algo depende del sistema operativo se dice.

## Los dos protagonistas

| Clase | Qué es |
|---|---|
| **`ProcessBuilder`** | **Configura** el proceso antes de lanzarlo: la orden, la carpeta de trabajo, las variables de entorno y las redirecciones. Se lanza con `start()` |
| **`Process`** | El hijo **ya en marcha**: su `pid()`, `waitFor()`, `exitValue()`, `destroy()` y sus flujos de entrada y salida |

!!! warning "El comando va troceado"
    Se pasa el programa y **cada argumento por separado**: `new ProcessBuilder("java", "Sumador.java", "20", "22")`. Nunca todo en una sola cadena (`"java Sumador.java 20 22"`): Java buscaría un programa que se llama así.

## Los programas hijo

Guarda estos programas en la carpeta de trabajo (cada uno en su archivo, con el nombre de la clase). Son los hijos que usan los ejemplos y los ejercicios de la unidad.

??? note "Sumador.java — suma dos números y responde con un código de salida"
    ```java
    public class Sumador {
        public static void main(String[] args) {
            int a = Integer.parseInt(args[0]);
            int b = Integer.parseInt(args[1]);
            int suma = a + b;
            System.out.println(a + " + " + b + " = " + suma);
            System.exit(suma > 100 ? 1 : 0);      // el código de salida cuenta algo al padre
        }
    }
    ```
    

??? note "Charlatan.java — escribe tres líneas normales y dos de error"
    ```java
    public class Charlatan {
        public static void main(String[] args) {
            System.out.println("linea 1 (salida normal)");
            System.err.println("ERROR: algo ha ido mal");
            System.out.println("linea 2 (salida normal)");
            System.err.println("ERROR: otro fallo");
            System.out.println("linea 3 (salida normal)");
        }
    }
    ```
    

??? note "Eco.java — repite en mayúsculas lo que le envíen por teclado"
    ```java
    import java.util.Scanner;
    
    public class Eco {
        public static void main(String[] args) {
            Scanner entrada = new Scanner(System.in);
            while (entrada.hasNextLine()) {
                System.out.println("ECO: " + entrada.nextLine().toUpperCase());
            }
            System.out.println("(fin de los datos)");
        }
    }
    ```
    

??? note "Dormilon.java — duerme 30 segundos"
    ```java
    public class Dormilon {
        public static void main(String[] args) throws InterruptedException {
            System.out.println("Dormilon: voy a dormir 30 segundos");
            Thread.sleep(30_000);
            System.out.println("Dormilon: me he despertado");
        }
    }
    ```
    

??? note "Contexto.java — cuenta cómo ha sido lanzado (entorno, carpeta y argumentos)"
    ```java
    import java.nio.file.Path;
    
    public class Contexto {
        public static void main(String[] args) {
            System.out.println("MODO = " + System.getenv("MODO"));
            System.out.println("Carpeta de trabajo: " + Path.of("").toAbsolutePath());
            System.out.println("Argumentos: " + String.join(", ", args));
        }
    }
    ```
    

??? note "Parlanchin.java — escribe cien mil líneas"
    ```java
    public class Parlanchin {
        public static void main(String[] args) {
            for (int i = 1; i <= 100_000; i++) {
                System.out.println("linea numero " + i);
            }
        }
    }
    ```
    

## Lanzar y esperar

El padre lanza el hijo, muestra su **PID**, **espera** con `waitFor()` y recoge su **código de salida**: por convención, **0 significa que todo fue bien** y cualquier otro número indica un problema (aquí, que la suma supera 100).

```java
import java.io.IOException;
import java.nio.file.Path;

public class Lanzar {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        for (String b : new String[]{"22", "90"}) {
            ProcessBuilder pb = new ProcessBuilder(java, "Sumador.java", "20", b);   // el comando va TROCEADO
            pb.inheritIO();                       // el hijo escribe en nuestra consola
            Process p = pb.start();
            System.out.println("PID del hijo: " + p.pid());
            int codigo = p.waitFor();             // espera a que termine
            System.out.println("Código de salida: " + codigo);
        }
    }
}
```

**Salida:**

```text
PID del hijo: 6884
20 + 22 = 42
Código de salida: 0
PID del hijo: 15632
20 + 90 = 110
Código de salida: 1
```

`inheritIO()` hace que el hijo escriba **en la misma consola** que el padre. La ruta de `java` se obtiene de `java.home`, para usar **la misma instalación** que ejecuta el padre.

!!! tip "Si el programa no existe"
    `start()` lanza **`IOException`** cuando no se puede lanzar (el programa no existe o no hay permisos). Es una excepción comprobada: hay que capturarla o declararla.

```java
import java.io.IOException;

public class NoExiste {
    public static void main(String[] args) throws InterruptedException {
        try {
            new ProcessBuilder("programa-que-no-existe").start();
        } catch (IOException e) {
            System.out.println("No se pudo lanzar el programa: " + e.getClass().getSimpleName());
        }
    }
}
```

**Salida:**

```text
No se pudo lanzar el programa: IOException
```

## Carpeta, entorno y argumentos

Antes de lanzarlo se puede decidir **dónde trabaja** el hijo (`directory(...)`) y qué **variables de entorno** tiene (`environment()` devuelve un mapa que se puede modificar; el cambio solo afecta al hijo):

```java
import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class ConContexto {
    public static void main(String[] args) throws IOException, InterruptedException {
        String java = Path.of(System.getProperty("java.home"), "bin", "java").toString();
        Path programa = Path.of("Contexto.java").toAbsolutePath();      // la ruta absoluta, porque el hijo trabajará en otra carpeta
        Files.createDirectories(Path.of("trabajo"));

        ProcessBuilder pb = new ProcessBuilder(java, programa.toString(), "uno", "dos");
        pb.directory(new File("trabajo"));            // carpeta de trabajo del hijo
        pb.environment().put("MODO", "prueba");       // variable de entorno solo para el hijo
        pb.inheritIO();
        pb.start().waitFor();
    }
}
```

**Salida:**

```text
MODO = prueba
Carpeta de trabajo: C:\Users\ana\proyecto\trabajo
Argumentos: uno, dos
```

## Que funcione en Windows y en Linux

Las órdenes del sistema cambian de un sistema a otro (por ejemplo, `dir` en Windows y `ls` en Linux, y varias son internas de `cmd`). Para que el programa sea **portable**, se mira `os.name` y se elige la orden:

```java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.util.List;

public class Portable {
    public static void main(String[] args) throws IOException, InterruptedException {
        boolean windows = System.getProperty("os.name").toLowerCase().startsWith("windows");
        List<String> orden = windows
                ? List.of("cmd", "/c", "echo", "Hola")      // echo es una orden interna de cmd
                : List.of("echo", "Hola");
        Process p = new ProcessBuilder(orden).start();
        System.out.println(new String(p.getInputStream().readAllBytes(), StandardCharsets.UTF_8).trim());
        System.out.println("Código de salida: " + p.waitFor());
    }
}
```

**Salida:**

```text
Hola
Código de salida: 0
```

## Qué recordar

* **`ProcessBuilder`** prepara y **`Process`** representa al hijo en marcha.
* El comando va **troceado**; `start()` puede lanzar `IOException`.
* `waitFor()` devuelve el **código de salida**: 0 = bien.
* `directory(...)` y `environment()` configuran **solo al hijo**.
* Las órdenes dependen del sistema: comprueba `os.name` si el programa debe funcionar en todos.

Siguiente: [A6.B Hablar con el proceso hijo](t-flujos.md).
