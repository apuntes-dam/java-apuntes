# 1.2 Práctica con el lenguaje

## Estructura de un programa Java

```java
// 1. Paquete e importaciones
import java.util.Scanner;

// 2. Clase (el archivo se llama igual: Saludo.java)
public class Saludo {

    // 3. Punto de entrada
    public static void main(String[] args) {
        System.out.println("Hola, Java");
    }
}
```

Todo el código vive dentro de una **clase**. La ejecución empieza en `public static void main(String[] args)`. Las sentencias terminan en `;` y los bloques van entre `{ }`.

## Variables

```java
int edad = 20;
double altura = 1.68;
boolean activo = true;
char letra = 'A';
String nombre = "Ana";
var ciudad = "Cádiz";   // inferencia de tipo local (Java 10+)
```

Una variable debe **declararse con tipo** y **asignarse antes de usarse**.

## Constantes: `final`

```java
final double IVA = 0.21;
// IVA = 0.10;  // error de compilación
```

Por convención, las constantes se escriben en MAYÚSCULAS. A nivel de clase suelen ser `public static final`.

## Literales

`42` (int), `42L` (long), `3.14` (double), `3.14f` (float), `'a'` (char), `"hola"` (String), `true` (boolean).

## Operadores

```java
public class Operadores {
    public static void main(String[] args) {
        System.out.println(7 + 2);   // 9
        System.out.println(7 / 2);   // 3   (int / int = división entera)
        System.out.println(7 / 2.0); // 3.5
        System.out.println(7 % 2);   // 1
        System.out.println(3 > 2 && 2 > 1); // true
        int x = 5;
        x += 2;                      // 7
        x++;                         // 8
        System.out.println(x);
    }
}
```

Grupos: aritméticos (`+ - * / %`), relacionales (`== != < > <= >=`), lógicos (`&& || !`), asignación (`= += -= ++ --`) y ternario (`cond ? a : b`).

!!! warning "Cadenas: usa `equals`"
    `==` compara referencias. Para comparar el contenido de dos `String` se usa `a.equals(b)`.

## Entrada y salida

```java
import java.util.Scanner;

public class Entrada {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("¿Cómo te llamas? ");
        String nombre = teclado.nextLine();
        System.out.println("Hola, " + nombre);
        System.out.printf("Tienes %d años%n", 20);
    }
}
```

### Leer números y otros tipos

`Scanner` tiene un método para cada tipo (`nextInt()`, `nextDouble()`...), pero tiene **dos trampas** que dan problemas a casi todo el mundo (más abajo). Por eso lo más seguro es **leer siempre la línea entera** con `nextLine()` y **convertirla** después:

| Quiero leer… | Cómo se escribe | Tipo que queda |
|---|---|---|
| Un texto | `String texto = teclado.nextLine();` | `String` |
| Un entero | `int n = Integer.parseInt(teclado.nextLine());` | `int` |
| Un decimal | `double d = Double.parseDouble(teclado.nextLine());` | `double` |
| Un sí/no | `boolean acepta = teclado.nextLine().trim().equalsIgnoreCase("s");` | `boolean` |
| Varios números en una línea | `int a = teclado.nextInt(); int b = teclado.nextInt();` | `int` |

Los cuatro primeros, en un programa:

```java
import java.util.Scanner;

public class Entrada {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);

        System.out.println("Edad:");
        int edad = Integer.parseInt(teclado.nextLine());              // int
        System.out.println("Altura en metros:");
        double altura = Double.parseDouble(teclado.nextLine());       // double
        System.out.println("Nombre:");
        String nombre = teclado.nextLine();                           // String
        System.out.println("¿Aceptas las condiciones? (s/n):");
        boolean acepta = teclado.nextLine().trim().equalsIgnoreCase("s");   // boolean

        System.out.println(nombre + " tiene " + edad + " años y mide " + altura + " m");
        System.out.println("El año que viene tendrá " + (edad + 1));
        System.out.println("Acepta: " + acepta);
    }
}
```

Con esta entrada: `17`, `1.75`, `Ana` y `s` (una por línea).

```text
Edad:
Altura en metros:
Nombre:
¿Aceptas las condiciones? (s/n):
Ana tiene 17 años y mide 1.75 m
El año que viene tendrá 18
Acepta: true
```

!!! warning "Trampa 1: `nextInt()` y después `nextLine()`"
    `nextInt()` lee el número pero **deja sin leer el salto de línea** que escribiste después. Si a continuación usas `nextLine()`, lee **lo que queda de esa línea: nada**.

    Con este programa y la entrada `17` y `Ana`:

    ```java
    import java.util.Scanner;

    public class Entrada {
        public static void main(String[] args) {
            Scanner teclado = new Scanner(System.in);
            int edad = teclado.nextInt();
            String nombre = teclado.nextLine();      // lee lo que QUEDA de la línea del 17: nada
            System.out.println("Edad: " + edad + " | Nombre: [" + nombre + "]");
        }
    }
    ```

    ```text
    Edad: 17 | Nombre: []
    ```

    El nombre sale **vacío**. Leyendo la línea entera y convirtiendo, se arregla:

    ```java
    import java.util.Scanner;

    public class Entrada {
        public static void main(String[] args) {
            Scanner teclado = new Scanner(System.in);
            int edad = Integer.parseInt(teclado.nextLine());   // se lee la línea ENTERA y se convierte
            String nombre = teclado.nextLine();
            System.out.println("Edad: " + edad + " | Nombre: [" + nombre + "]");
        }
    }
    ```

    ```text
    Edad: 17 | Nombre: [Ana]
    ```


!!! warning "Trampa 2: `nextDouble()` depende del idioma del equipo"
    `nextDouble()` usa la **configuración regional**. En un equipo con idioma **español**, espera la **coma** (`1,75`) y **falla con el punto** (`1.75`); en uno configurado en inglés espera el punto. Estos son los resultados reales con el idioma del equipo en español:

    ```java
    import java.util.Scanner;

    public class Entrada {
        public static void main(String[] args) {
            Scanner teclado = new Scanner(System.in);
            double altura = teclado.nextDouble();
            System.out.println("Altura: " + altura);
        }
    }
    ```

    Con `1.75`:

    ```text
    Exception in thread "main" java.util.InputMismatchException
    ```

    Con `1,75` (coma) sí funciona:

    ```text
    Altura: 1.75
    ```

    Para que el programa se comporte igual en **cualquier** equipo, se lee la línea y se usa `Double.parseDouble`, que **siempre** espera el punto:

    ```java
    import java.util.Scanner;

    public class Entrada {
        public static void main(String[] args) {
            Scanner teclado = new Scanner(System.in);
            double altura = Double.parseDouble(teclado.nextLine());   // siempre con punto, sea cual sea el idioma del equipo
            System.out.println("Altura: " + altura);
        }
    }
    ```

    Con `1.75`:

    ```text
    Altura: 1.75
    ```


!!! note "Si escriben otra cosa"
    `Integer.parseInt("abc")` lanza `NumberFormatException`, y `nextInt()` lanza `InputMismatchException`. Cómo controlarlas con `try/catch` está en [1.3 Tipos de datos](03-tipos.md).

Las salidas de esta sección se han obtenido **ejecutando cada programa con Java y la entrada indicada**.

## Comentarios

```java
// Comentario de una línea
/* Comentario
   de varias líneas */
/** Comentario Javadoc: genera documentación */
```

## Crear un proyecto y usar el IDE

IDEs habituales: IntelliJ IDEA, Eclipse, NetBeans y VS Code (con *Extension Pack for Java*). Para proyectos con dependencias se usa Maven o Gradle.
