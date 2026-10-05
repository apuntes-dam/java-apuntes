# 1.6 Bucles: `for` y `forEach`

Un bucle repite un bloque de código. En Java hay dos formas muy usadas de recorrer valores: el `for` de toda la vida y el método `forEach` con una lambda.

## Versión 1: `for` donde se ve de dónde sale cada cosa

```java
public class ForSimple {
    public static void main(String[] args) {
        for (int i = 1; i <= 5; i++) {
            System.out.println("Número: " + i);
        }
    }
}
```

| Parte | Qué hace |
|---|---|
| `int i = 1` | **De dónde sale**: `i` nace valiendo 1 |
| `i <= 5` | **Hasta cuándo**: se repite mientras sea cierto |
| `i++` | **Cómo avanza**: suma 1 al terminar cada vuelta |
| `{ ... }` | Lo que se repite |

## Versión 2: sin `for` completo, con `forEach`

Java no tiene rangos como `1..5`, así que se parte de una lista:

```java
import java.util.List;

public class ForEachSimple {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(1, 2, 3, 4, 5);

        numeros.forEach(numero -> {
            System.out.println("Número actual: " + numero);
        });
    }
}
```

`numero -> { ... }` es una **lambda**: se ejecuta una vez por cada elemento y `numero` es el elemento de esa vuelta.

Con una sola línea se puede acortar usando una referencia a método:

```java
numeros.forEach(System.out::println);
```

Con lógica añadida:

```java
numeros.forEach(numero -> {
    if (numero % 2 == 0) {
        System.out.println(numero + " es par");
    } else {
        System.out.println(numero + " es impar");
    }
});
```

## Comparación rápida

| Característica | `for` | `forEach` |
|---|---|---|
| Estilo | Tradicional | Funcional (lambda) |
| `break` / `continue` | ✅ Sí | ❌ No (un `return` dentro solo salta a la siguiente vuelta) |
| Modificar una variable local de fuera | ✅ Sí | ❌ Solo si es *efectivamente final* |
| Legibilidad | Muy clara en bucles simples | Mejor con operaciones encadenadas |

!!! tip "Regla práctica"
    Empieza con `for`. Usa `forEach` cuando solo quieras hacer algo con cada elemento y no necesites cortar el bucle.

## Variantes

```java
import java.util.List;

public class Variantes {
    public static void main(String[] args) {
        // Descendente
        for (int i = 5; i >= 1; i--) {
            System.out.println(i);
        }

        // Con paso de 2
        for (int i = 1; i <= 10; i += 2) {
            System.out.println(i);
        }

        // Sin incluir el último valor
        for (int i = 1; i < 5; i++) {
            System.out.println(i);
        }

        // for-each: recorrer una lista
        List<String> nombres = List.of("Ana", "Luis", "Eva");
        for (String nombre : nombres) {
            System.out.println(nombre);
        }

        // Recorrer con índice
        for (int i = 0; i < nombres.size(); i++) {
            System.out.println(i + ": " + nombres.get(i));
        }
    }
}
```

## Cortar un bucle con una variable de control

Una forma sencilla de parar un bucle sin `break`: una variable booleana (una *bandera*) que el bucle consulta en su condición. Cuando pasa lo que buscas, la pones en `false`.

```java
public class Bandera {
    public static void main(String[] args) {
      boolean activo = true;
      int i = 1;

      while (activo) {
        System.out.println(i);
        if (i == 5) {
          activo = false; // se cumple la condición: el bucle se detiene
        }
        i++;
      }
    }
}
```

Con `do-while`, que se ejecuta al menos una vez:

```java
public class BanderaDoWhile {
    public static void main(String[] args) {
      boolean activo = true;
      int intentos = 0;

      do {
        intentos++;
        System.out.println("Intento " + intentos);
        if (intentos == 3) {
          activo = false;
        }
      } while (activo);
    }
}
```

!!! tip "Por qué esta forma"
    La condición del bucle muestra de un vistazo cuándo termina, y funciona igual en Java, Dart y Kotlin.

??? note "Opcional: `break` y `continue`"
    Existen, pero no son imprescindibles: casi siempre se puede escribir lo mismo con una variable de control.

    | Palabra | Qué hace |
    |---|---|
    | `break` | Sale del bucle de golpe |
    | `continue` | Salta a la siguiente vuelta |

    ```java
    for (int i = 1; i <= 10; i++) {
      if (i == 3) continue; // salta esta vuelta
      if (i == 6) break;    // sale del bucle
      System.out.println(i); // 1, 2, 4, 5
    }
    ```

## `while` y `do-while`

```java
int cuenta = 3;
while (cuenta > 0) {   // comprueba antes
    System.out.println(cuenta--);
}

int intentos = 0;
do {                   // se ejecuta al menos una vez
    intentos++;
} while (intentos < 3);
System.out.println(intentos); // 3
```
