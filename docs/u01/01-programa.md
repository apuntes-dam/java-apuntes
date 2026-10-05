# 1.1 Un programa

Un **programa** es una secuencia de instrucciones que un ordenador ejecuta para resolver un problema. Antes de escribirlo hay que tener claro el **algoritmo**: los pasos, en orden, que llevan de unos datos de entrada a un resultado.

## Ciclo de desarrollo

1. **Analizar** el problema: qué entra, qué debe salir.
2. **Diseñar** el algoritmo (pseudocódigo o diagrama).
3. **Codificar** en un lenguaje (aquí, Java).
4. **Compilar, probar** y corregir.
5. **Documentar** y mantener.

## Pseudocódigo

```text
ALGORITMO areaRectangulo
  LEER base
  LEER altura
  area <- base * altura
  ESCRIBIR area
FIN
```

## Del pseudocódigo a Java

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
| `LEER x` | `teclado.nextDouble()` / `nextInt()` / `nextLine()` |
| `x <- expresión` | `x = expresión;` |
| `ESCRIBIR x` | `System.out.println(x)` |

!!! warning "Errores típicos al diseñar"
    Olvidar un caso (por ejemplo, base cero), usar una variable sin inicializar (Java no compila) y mezclar tipos sin convertir.
