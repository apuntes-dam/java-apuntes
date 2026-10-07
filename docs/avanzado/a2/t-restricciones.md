# A2.B Restricciones y variación

## Restringir el tipo

Si una función genérica necesita **hacer algo** con sus valores, como compararlos o sumarlos, tiene que saber que existe esa operación. Se lo cuentas con una **restricción**.

En Java se restringe con **`extends`**, que aquí significa «es este tipo o uno que lo hereda o implementa»: `<T extends Comparable<T>>` acepta solo tipos comparables (`Integer`, `Double`, `String`...), y gracias a eso se puede llamar a `compareTo`. Sin restricción, de `T` solo se sabe que es un `Object`.

```java
import java.util.List;
import java.util.function.Predicate;

// Restricciones: «T extends ...» limita qué tipos se aceptan, y así se pueden usar sus métodos.
public class Limites {
    // Solo tipos que se pueden comparar entre sí (Integer, Double, String...): por eso existe compareTo
    static <T extends Comparable<T>> T maximo(List<T> lista) {
        T mayor = lista.get(0);
        for (T x : lista) {
            if (x.compareTo(mayor) > 0) mayor = x;
        }
        return mayor;
    }

    // «? extends Number»: una lista de cualquier subtipo de Number (Integer, Double...), solo para leer de ella
    static double sumar(List<? extends Number> lista) {
        double suma = 0;
        for (Number n : lista) suma += n.doubleValue();
        return suma;
    }

    // Sin restricción: acepta cualquier T y una función que lo examina
    static <T> int contarSi(List<T> lista, Predicate<T> cumple) {
        int cuenta = 0;
        for (T x : lista) {
            if (cumple.test(x)) cuenta++;
        }
        return cuenta;
    }

    public static void main(String[] args) {
        System.out.println("maximo de [3, 9, 4]: " + maximo(List.of(3, 9, 4)));
        System.out.println("maximo de [pera, manzana, uva]: " + maximo(List.of("pera", "manzana", "uva")));
        System.out.println("suma de [1, 2, 3]: " + sumar(List.of(1, 2, 3)));
        System.out.println("suma de [0.5, 0.25]: " + sumar(List.of(0.5, 0.25)));
        System.out.println("pares en [1..6]: " + contarSi(List.of(1, 2, 3, 4, 5, 6), n -> n % 2 == 0));
        // maximo(List.of(new Object())) no compila: Object no es Comparable
    }
}
```

Salida:

```text
maximo de [3, 9, 4]: 9
maximo de [pera, manzana, uva]: uva
suma de [1, 2, 3]: 6.0
suma de [0.5, 0.25]: 0.75
pares en [1..6]: 3
```

| Función | Qué demuestra |
|---|---|
| `maximo` | Funciona con enteros **y** con textos, pero solo con tipos que se pueden comparar |
| `sumar` | Una restricción a **números**: acepta enteros y decimales |
| `contarSi` | **Sin** restricción: no necesita saber nada de `T`, porque delega en la función que recibe |

## ¿Una lista de enteros es una lista de números?

Parece que sí, pero depende de si la lista se puede **modificar**. Cada lenguaje lo resuelve distinto:

En Java los genéricos son **invariantes**: una `List<Integer>` **no** es una `List<Number>`, aunque `Integer` sea un `Number`. Si no, podrías meter un `Double` en una lista de enteros. Los **comodines** lo resuelven: `List<? extends Number>` acepta una lista de cualquier subtipo de `Number`, pero solo para **leer** de ella (*productor*). Con `List<? super Integer>` se puede **escribir** (*consumidor*).

!!! tip "Cómo decidir en Java"
    Pregúntate si tu función **solo lee** de la colección, solo **escribe** o hace las dos cosas. Si solo lee, acepta el tipo más amplio que puedas; si hace las dos, el tipo tiene que ser exacto.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Restringir demasiado (por ejemplo, pedir `ArrayList` cuando vale cualquier lista) | Pide la interfaz más general que necesites |
| Llamar a un método que la restricción no garantiza | Añade la restricción que lo garantice, o recibe una función como parámetro |
| Querer escribir en una colección que se ha recibido como «solo lectura» | Recibe una colección modificable del tipo exacto |

## Para practicar

Los ejercicios [A2.3 y A2.5](ejercicios.md) usan una restricción y una función como criterio. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
