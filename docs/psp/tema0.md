# Tema 0 · Lo que completa el repaso de Java

El **Tema 0** del módulo (*Repaso de Java*) recorre el Java de 1.º DAM. Casi todo está ya en esta web: la tabla de abajo dice **dónde**. Esta página añade lo que el tema pide y las unidades no detallan: `varargs`, la recursión y `StackOverflowError`, las trampas de los arrays, `yield`, las trampas de `Scanner`, `equals` y `hashCode`, `StringBuilder`, el formato de texto y la lectura de una traza. **Todos los ejemplos se han ejecutado con Java 25.**

## Dónde está cada apartado del Tema 0

| Apartado del Tema 0 | Dónde estudiarlo |
|---|---|
| 1. Fundamentos | [U1 · Primer programa](../u01/index.md) y [U2](../u02/index.md); lo que falta, en esta página |
| 2. Métodos y arrays | [U1](../u01/index.md), [U3 · Listas y tuplas](../u03/01-listas.md); lo que falta, en esta página |
| 3. Cadenas de texto | [U3 · Cadenas](../u03/00-cadenas.md); lo que falta, en esta página |
| 4. Excepciones | [U2 · Excepciones](../u02/02-excepciones.md) |
| 5. Programación orientada a objetos | [U4](../u04/index.md), [U5](../u05/index.md) y [U6](../u06/index.md) |
| 6. Colecciones | [U3 · Listas](../u03/01-listas.md), [Mapas](../u03/02-mapas.md) y [Conjuntos](../u03/03-conjuntos.md) |
| 7. Java moderno (lambdas, streams, `Optional`, `enum`, `record`) | [A1 · Programación funcional](../avanzado/a1/index.md) y [U4](../u04/index.md) |
| 8. Ficheros y entrada/salida estándar | [U7 · Entrada/salida](../u07/index.md) y [7.E](../u07/t-path.md) |

Para comprobar lo que sabes de todo el tema hay un [simulador de examen tipo test](simulador.md).

## Métodos con número variable de argumentos (`varargs`)

Un parámetro `tipo... nombre` acepta **cero o más** argumentos, y dentro del método es un **array**. Tiene que ser **el último** parámetro. `String.format` y `printf` lo usan.

```java
public class Varargs {
    static int suma(int... numeros) {         // dentro, numeros es un int[]
        int total = 0;
        for (int n : numeros) {
            total += n;
        }
        return total;
    }

    static String describir(String titulo, int... valores) {      // el varargs va el último
        return titulo + " (" + valores.length + " valores): " + suma(valores);
    }

    public static void main(String[] args) {
        System.out.println(suma());
        System.out.println(suma(3, 4));
        System.out.println(suma(1, 2, 3, 4));
        System.out.println(suma(new int[]{10, 20}));       // también se puede pasar un array
        System.out.println(describir("Notas", 7, 9, 8));
        System.out.println(String.format("%s tiene %d años", "Luis", 20));      // String.format también es varargs
    }
}
```

**Salida:**

```text
0
7
10
30
Notas (3 valores): 24
Luis tiene 20 años
```

## Recursión: el caso base y `StackOverflowError`

Un método recursivo se llama a sí mismo y necesita siempre un **caso base** que detenga las llamadas. Cada llamada ocupa espacio en la **pila** del hilo; sin caso base, la pila se agota y la JVM lanza `StackOverflowError`. Es un `Error`, no una `Exception`: no se captura en programas reales, aquí se hace solo para verlo.

```java
public class Recursion {
    static long factorial(int n) {
        if (n <= 1) return 1;                // caso base
        return n * factorial(n - 1);         // caso recursivo
    }

    static int sinCasoBase(int n) {
        return sinCasoBase(n + 1);           // nunca se detiene
    }

    public static void main(String[] args) {
        System.out.println(factorial(5));
        System.out.println(factorial(20));
        try {
            sinCasoBase(0);
        } catch (StackOverflowError e) {     // es un Error, no una Exception: aquí solo se captura para mostrarlo
            System.out.println("La pila se ha agotado: " + e.getClass().getSimpleName());
        }
    }
}
```

**Salida:**

```text
120
2432902008176640000
La pila se ha agotado: StackOverflowError
```

(En el Tema 2 verás que **cada hilo tiene su propia pila**.)

## Arrays: tres trampas

* Un array se pasa a un método **por valor de su referencia**: el método no puede cambiar a qué array apunta tu variable, pero sí **el contenido**.
* `int[] b = a;` **no copia** el array: `a` y `b` son dos nombres del mismo. Para copiar, `Arrays.copyOf` o `a.clone()`.
* `System.out.println(a)` no muestra el contenido: hay que usar `Arrays.toString` (o `Arrays.deepToString` en matrices).

```java
import java.util.Arrays;

public class Arrays1 {
    static void duplicar(int x) { x = x * 2; }
    static void duplicar(int[] v) { v[0] = v[0] * 2; }

    public static void main(String[] args) {
        int n = 5;
        int[] a = {5, 1, 4};
        duplicar(n);
        duplicar(a);
        System.out.println("n = " + n + ", a = " + Arrays.toString(a));

        int[] b = a;                         // NO es una copia: dos nombres para el mismo array
        b[1] = 99;
        System.out.println("a[1] = " + a[1]);

        int[] copia = Arrays.copyOf(a, a.length);
        copia[1] = 0;
        System.out.println("a[1] = " + a[1] + ", copia[1] = " + copia[1]);

        Arrays.sort(a);
        System.out.println(Arrays.toString(a) + " -> posición del 10: " + Arrays.binarySearch(a, 10));

        int[][] m = {{1, 2, 3}, {4, 5, 6}};
        System.out.println(m.length + " filas, " + m[0].length + " columnas: " + Arrays.deepToString(m));

        System.out.println(a);               // OJO: no muestra el contenido (el número tras @ cambia en cada ejecución)
    }
}
```

**Salida:**

```text
n = 5, a = [10, 1, 4]
a[1] = 99
a[1] = 99, copia[1] = 0
[4, 10, 99] -> posición del 10: 1
2 filas, 3 columnas: [[1, 2, 3], [4, 5, 6]]
[I@6b4a4e18
```

Y los errores típicos con índices y tamaños:

```java
public class FueraDeRango {
    public static void main(String[] args) {
        int[] notas = new int[3];
        try {
            notas[3] = 10;
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println(e.getMessage());
        }
        String[] dias = new String[2];
        System.out.println(dias[0]);                     // las referencias empiezan en null
        System.out.println(dias.length + " " + "abc".length());      // atributo frente a método
    }
}
```

**Salida:**

```text
Index 3 out of bounds for length 3
null
2 3
```

## `switch` con bloques y `yield`

El `switch` con flecha no necesita `break`, no «cae» al caso siguiente y **devuelve un valor**. Si un caso necesita varias instrucciones, se usa un bloque y `yield` para devolver el valor:

```java
public class Conmutador {
    static int diasDelMes(int mes, int anio) {
        return switch (mes) {
            case 2 -> {
                boolean bisiesto = (anio % 4 == 0 && anio % 100 != 0) || anio % 400 == 0;
                yield bisiesto ? 29 : 28;           // yield devuelve el valor de un bloque
            }
            case 4, 6, 9, 11 -> 30;
            default -> 31;
        };
    }

    public static void main(String[] args) {
        System.out.println(diasDelMes(2, 2024));
        System.out.println(diasDelMes(2, 2100));
        System.out.println(diasDelMes(4, 2025));
        System.out.println(diasDelMes(12, 2025));

        int dia = 6;
        String tipo = switch (dia) {
            case 1, 2, 3, 4, 5 -> "Laborable";
            case 6, 7 -> "Fin de semana";
            default -> "Día no válido";
        };
        System.out.println(tipo);
    }
}
```

**Salida:**

```text
29
28
30
31
Fin de semana
```

## Las trampas de `Scanner`

* `nextInt()` y `nextDouble()` leen el número pero **dejan el salto de línea** sin leer: un `nextLine()` posterior devuelve la cadena vacía. La solución es leer siempre con `nextLine()` y convertir con `Integer.parseInt(...)`, o descartar el resto de la línea.
* `Scanner` usa la **configuración regional del sistema**: en español espera `3,5`. Con `sc.useLocale(Locale.US)` acepta `3.5`.

Con esta entrada (`19`, `Lucía` y `1.68`, cada una en su línea):

```java
import java.util.Locale;
import java.util.Scanner;

public class Trampa {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        sc.useLocale(Locale.US);                        // acepta 3.5 aunque el sistema esté en español
        int edad = sc.nextInt();
        String resto = sc.nextLine();                   // lo que quedaba de la línea de la edad
        System.out.println("nextLine() devolvió: [" + resto + "]");
        String nombre = sc.nextLine();
        double altura = sc.nextDouble();
        System.out.println(nombre + ", " + edad + " años, " + altura + " m");
    }
}
```

**Salida:**

```text
nextLine() devolvió: []
Lucía, 19 años, 1.68 m
```

Fíjate en que `nextLine()` tras `nextInt()` devolvió una cadena vacía (entre corchetes no hay nada) y que, gracias a ese `nextLine()` extra, el nombre se lee bien.

## `equals` y `hashCode`

`==` compara **referencias**. Para que dos objetos con los mismos datos se consideren iguales hay que sobrescribir `equals`, y **siempre que se sobrescriba `equals` hay que sobrescribir también `hashCode`**: `HashSet` y `HashMap` usan los dos para encontrar los elementos. Un `record` los genera solo.

```java
import java.util.HashSet;
import java.util.Objects;
import java.util.Set;

public class Iguales {
    static class Alumno {
        private final String nombre;

        Alumno(String nombre) {
            this.nombre = nombre;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Alumno otro)) return false;
            return nombre.equals(otro.nombre);
        }

        @Override
        public int hashCode() {
            return Objects.hash(nombre);
        }
    }

    record Punto(int x, int y) { }                  // un record ya trae equals y hashCode

    public static void main(String[] args) {
        Alumno a = new Alumno("Ana");
        Alumno b = new Alumno("Ana");
        System.out.println(a == b);                  // distintos objetos
        System.out.println(a.equals(b));             // mismo contenido
        System.out.println(a.hashCode() == b.hashCode());

        Set<Alumno> conjunto = new HashSet<>();
        conjunto.add(a);
        conjunto.add(b);
        System.out.println(conjunto.size());         // 1: el conjunto los considera el mismo

        System.out.println(new Punto(1, 2).equals(new Punto(1, 2)));
    }
}
```

**Salida:**

```text
false
true
true
1
true
```

## Texto: `StringBuilder`, formato y bloques de texto

```java
import java.util.Locale;

public class Texto {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        for (int i = 1; i <= 5; i++) {
            sb.append(i).append(", ");
        }
        sb.setLength(sb.length() - 2);                          // quita la última ", "
        System.out.println(sb);
        System.out.println(new StringBuilder("hola").reverse());

        System.out.println(String.format(Locale.US, "%-8s|%6.2f|%05d", "Café", 1.2, 42));
        System.out.println("%s tiene %d años".formatted("Luis", 20));

        String menu = """
                1. Nuevo pedido
                2. Listar pedidos
                0. Salir
                """;
        System.out.print(menu);

        System.out.println("a.b.c".split("\\.").length + " " + "a  b   c".split("\\s+").length);
    }
}
```

**Salida:**

```text
1, 2, 3, 4, 5
aloh
Café    |  1.20|00042
Luis tiene 20 años
1. Nuevo pedido
2. Listar pedidos
0. Salir
3 3
```

* `StringBuilder` se usa para construir texto por partes dentro de un bucle (`String` es inmutable).
* `%-8s` alinea a la izquierda, `%6.2f` da 2 decimales en un ancho de 6, `%05d` rellena con ceros. Con `Locale.US` el decimal lleva punto.
* Un bloque de texto (`"""`) respeta los saltos de línea y quita la sangría común.
* `split` usa **expresiones regulares**: para separar por un punto hay que escribir `"\\."`, y para separar por uno o más espacios, `"\\s+"`.

## Leer una traza de pila

Cuando nadie captura una excepción, el programa termina y la JVM escribe la **traza**, que se lee **de arriba abajo**: el tipo de excepción, el mensaje y la línea donde se produjo. Además, el programa termina con **código de salida 1** (si no hay error, es 0).

```java
public class Traza {
    static int convertir(String texto) {
        return Integer.parseInt(texto);
    }

    public static void main(String[] args) {
        System.out.println(convertir("42"));
        System.out.println(convertir("abc"));        // no se captura: la traza muestra tipo, mensaje y línea
    }
}
```

Al ejecutarlo, por la salida estándar sale `42` y por la salida de error, la traza (con Java 25; los números de línea de la propia JDK cambian de una versión a otra):

```text
Exception in thread "main" java.lang.NumberFormatException: For input string: "abc"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
	at java.base/java.lang.Integer.parseInt(Integer.java:565)
	at java.base/java.lang.Integer.parseInt(Integer.java:662)
	at Traza.convertir(Traza.java:3)
	at Traza.main(Traza.java:8)
```

La primera línea dice el **tipo** y el **mensaje**; las siguientes, la cadena de llamadas, y la que importa es la primera de **tu** código: `Traza.convertir(Traza.java:3)`, el método y la línea donde se produjo el error. El programa terminó con código de salida **1**.
## Para seguir

* [Simulador de examen](simulador.md) (tipo test, con preguntas de «qué muestra este programa» y de completar una línea).
* El Tema 1 (procesos) está en la [página de entrada de PSP](index.md).
