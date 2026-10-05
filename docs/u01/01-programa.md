# 1.1 Un programa

Un **programa** es una secuencia de instrucciones que resuelve un problema, y el **algoritmo** son los pasos, en orden, que van de los datos de entrada al resultado.

!!! info "La teoría general está aparte"
    Qué es un algoritmo, el ciclo de desarrollo y el **pseudocódigo** son iguales en todos los lenguajes, así que están en una sola página: [Fundamentos: algoritmos y pseudocódigo](https://dopemmanuel.github.io/apuntes-lenguajes/fundamentos/). Aquí solo ves cómo se traduce a Java.

## Del pseudocódigo a Java

El algoritmo del área de un rectángulo, en pseudocódigo:

```text
ALGORITMO areaRectangulo
  LEER base
  LEER altura
  area <- base * altura
  ESCRIBIR "Área:", area
FIN
```

y su traducción a Java:

```java
import java.util.Scanner;

public class AreaRectangulo {
    public static void main(String[] args) {
        Scanner teclado = new Scanner(System.in);
        System.out.print("Base: ");
        double base = teclado.nextDouble();
        System.out.print("Altura: ");
        double altura = teclado.nextDouble();

        double area = base * altura;
        System.out.println("Área: " + area);
    }
}
```

| Pseudocódigo | Java |
|---|---|
| `LEER x` | `teclado.nextDouble()`, `nextInt()` o `nextLine()` (con `Scanner`) |
| `x <- expresión` | `x = expresión;` |
| `ESCRIBIR x` | `System.out.println(x)` |
| `SI ... SINO ... FIN SI` | `if` / `else` (ver [1.2](02-lenguaje.md)) |
| `PARA` / `MIENTRAS` | `for` / `while` (ver [1.6](06-bucles.md)) |

!!! warning "Errores típicos en Java"
    Dejar una variable sin inicializar (Java no compila) y olvidar que `int / int` es división entera. Los errores generales de diseño (olvidar un caso, bucles que no terminan…) están en [Fundamentos](https://dopemmanuel.github.io/apuntes-lenguajes/fundamentos/).
