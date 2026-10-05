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

## Comentarios

```java
// Comentario de una línea
/* Comentario
   de varias líneas */
/** Comentario Javadoc: genera documentación */
```

## Crear un proyecto y usar el IDE

IDEs habituales: IntelliJ IDEA, Eclipse, NetBeans y VS Code (con *Extension Pack for Java*). Para proyectos con dependencias se usa Maven o Gradle.
