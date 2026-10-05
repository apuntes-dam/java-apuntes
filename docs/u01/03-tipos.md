# 1.3 Tipos de datos

Java es de **tipado estático**: cada variable tiene un tipo fijo que se comprueba al compilar.

## Tipos primitivos

| Tipo | Tamaño | Ejemplo |
|---|---|---|
| `byte` | 8 bits | `byte b = 10;` |
| `short` | 16 bits | `short s = 1000;` |
| `int` | 32 bits | `int i = 42;` |
| `long` | 64 bits | `long l = 10_000_000_000L;` |
| `float` | 32 bits | `float f = 3.14f;` |
| `double` | 64 bits | `double d = 3.14;` |
| `char` | 16 bits | `char c = 'A';` |
| `boolean` | — | `boolean ok = true;` |

## Tipos de referencia

`String`, arrays, clases y colecciones. Su valor por defecto es `null`.

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Colecciones {
    public static void main(String[] args) {
        String s = "Java";
        System.out.println(s.length());       // 4
        System.out.println(s.toUpperCase());  // JAVA

        int[] numeros = {3, 1, 2};            // array de tamaño fijo
        List<Integer> lista = new ArrayList<>();
        lista.add(3);
        lista.add(1);
        Map<String, Integer> mapa = new HashMap<>();
        mapa.put("a", 1);

        System.out.println(numeros.length + " " + lista + " " + mapa.get("a"));
    }
}
```

## Conversiones

```java
public class Conversiones {
    public static void main(String[] args) {
        // Implícita (ensanchamiento): de menor a mayor rango
        int i = 5;
        double d = i;                 // 5.0

        // Explícita (casting): puede perder información
        double pi = 3.99;
        int entero = (int) pi;        // 3 (trunca)

        // Texto <-> número
        int n = Integer.parseInt("42");
        double x = Double.parseDouble("3.5");
        String t = String.valueOf(42);

        System.out.println(d + " " + entero + " " + n + " " + x + " " + t);
    }
}
```

!!! warning "`NumberFormatException`"
    `Integer.parseInt("abc")` lanza una excepción. Hay que controlarla con `try/catch` cuando el dato viene del usuario.

## Wrappers

Cada primitivo tiene una clase asociada (`int` → `Integer`, `double` → `Double`) necesaria, por ejemplo, para las colecciones genéricas. El paso entre ambos es automático (*autoboxing*).
