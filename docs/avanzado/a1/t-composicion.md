# A1.C Componer, esperar e inmutabilidad

Tres ideas que completan la programación funcional: **componer** funciones pequeñas en otras mayores, **calcular solo lo necesario** (evaluación perezosa) y **no modificar los datos** (inmutabilidad).

## Componer funciones

Si tienes funciones pequeñas que hacen una cosa cada una, puedes unirlas: la salida de una es la entrada de la siguiente. Eso es una **tubería**.

`Function` trae dos métodos para componer: `f.andThen(g)` hace primero `f` y luego `g`; `f.compose(g)` hace primero `g` y luego `f`. Con una lista de funciones, `reduce(Function.identity(), Function::andThen)` las junta en una sola. La **aplicación parcial** no existe como tal: se escribe una lambda que fija un argumento (`b -> sumar.apply(5, b)`).

```java
import java.util.function.BiFunction;
import java.util.function.Function;

// Funciones de orden superior: componer funciones, encadenarlas, fijar argumentos y devolver funciones.
public class Composicion {
    public static void main(String[] args) {
        Function<Integer, Integer> doble = n -> n * 2;
        Function<Integer, Integer> sumar3 = n -> n + 3;

        // compose(g): primero g, luego esta; andThen(g): primero esta, luego g
        System.out.println("componer(doble, sumar3)(4) = " + doble.compose(sumar3).apply(4));

        // una "tubería": se encadenan funciones con andThen
        Function<String, String> recortar = String::strip;
        Function<String, String> minusculas = String::toLowerCase;
        Function<String, String> guiones = s -> s.replaceAll("\\s+", "-");
        Function<String, String> slug = recortar.andThen(minusculas).andThen(guiones);
        System.out.println("slug: \"  Hola Mundo Cruel  \" -> \"" + slug.apply("  Hola Mundo Cruel  ") + "\"");

        // con una lista de pasos de longitud variable: reduce junta todas las funciones en una
        // List<Function<String, String>> pasos = List.of(recortar, minusculas, guiones);
        // Function<String, String> slug = pasos.stream().reduce(Function.identity(), Function::andThen);

        // aplicación parcial: fijar el primer argumento de una función de dos
        BiFunction<Integer, Integer, Integer> sumar = (a, b) -> a + b;
        Function<Integer, Integer> sumar5 = b -> sumar.apply(5, b);
        System.out.println("sumar5(10) = " + sumar5.apply(10));

        // función que devuelve otra función (currying): potencia(base)(exponente)
        Function<Integer, Function<Integer, Integer>> potencia = base -> exp -> (int) Math.pow(base, exp);
        System.out.println("potencia(2)(10) = " + potencia.apply(2).apply(10));
    }
}
```

Salida:

```text
componer(doble, sumar3)(4) = 14
slug: "  Hola Mundo Cruel  " -> "hola-mundo-cruel"
sumar5(10) = 15
potencia(2)(10) = 1024
```

La función `slug` convierte un título en una dirección web: quita los espacios de los extremos, pasa a minúsculas y cambia los espacios por guiones. Cada paso es una función de una línea, fácil de probar por separado.

## Calcular solo lo necesario

Una colección **ansiosa** calcula todos sus elementos al crearse. Una **perezosa** calcula cada elemento cuando alguien lo pide, y por eso puede ser **infinita**. En Java:

Los *streams* son perezosos: nada se calcula hasta la operación terminal. `Stream.iterate` crea una secuencia **infinita** y `limit` la corta. Fíjate en que cada elemento recorre **toda la tubería** antes de pasar al siguiente, y que se deja de pedir en cuanto se tienen los tres que se necesitaban.

```java
import java.util.List;
import java.util.stream.Stream;

// Evaluación perezosa: los valores se calculan solo cuando alguien los pide.
public class Perezoso {
    public static void main(String[] args) {
        // Stream.iterate crea una secuencia infinita; filter y map no calculan nada todavía
        Stream<Integer> pares = Stream.iterate(1, n -> n + 1)
                .filter(n -> {
                    System.out.println("revisando " + n);
                    return n % 2 == 0;
                })
                .map(n -> n * n);
        System.out.println("(se define la secuencia: todavía no se ha calculado nada)");

        // limit(3) es la operación terminal: pide valores uno a uno hasta tener tres
        List<Integer> primeros = pares.limit(3).toList();
        System.out.println("primeros 3 pares al cuadrado: " + primeros);

        // cada elemento se calcula a partir del anterior: pares (a, b) -> (b, a + b)
        List<Long> fibonacci = Stream.iterate(new long[]{0, 1}, f -> new long[]{f[1], f[0] + f[1]})
                .map(f -> f[0])
                .limit(8)
                .toList();
        System.out.println("fibonacci: " + fibonacci);
    }
}
```

Salida:

```text
(se define la secuencia: todavía no se ha calculado nada)
revisando 1
revisando 2
revisando 3
revisando 4
revisando 5
revisando 6
primeros 3 pares al cuadrado: [4, 16, 36]
fibonacci: [0, 1, 1, 2, 3, 5, 8, 13]
```

Mira el orden de la salida. Primero aparece el aviso de que la secuencia está definida pero sin calcular; después, `revisando 1` a `revisando 6`: solo se revisaron los números necesarios para encontrar **tres pares**, y ni uno más. Eso es la evaluación perezosa.

## No modificar los datos

Una función es **pura** si su resultado depende solo de sus argumentos y no cambia nada fuera de ella. Las puras son más fáciles de entender y de probar, y también de usar **a la vez** en varios hilos (lo verás en A3). La forma de conseguirlo con datos es la **inmutabilidad**: en vez de cambiar un objeto, se crea otro con el cambio.

Un **`record`** es inmutable por definición: sus campos son `final` y no tiene *setters*. Para «cambiar» algo se crea otro objeto (`conPuntos`). `List.of(...)` devuelve una lista inmodificable: `add` o `remove` lanzan `UnsupportedOperationException`.

```java
import java.util.List;
import java.util.stream.Stream;

// Inmutabilidad: en vez de modificar un objeto, se crea una copia con el cambio.
public class Inmutable {
    // un record es inmutable: sus campos son final y no hay setters
    record Jugador(String nombre, int puntos) {
        // devuelve un Jugador nuevo; el original no cambia
        Jugador conPuntos(int nuevos) {
            return new Jugador(nombre, nuevos);
        }
    }

    static String describir(Jugador j) {
        return j.nombre() + " tiene " + j.puntos() + " puntos";
    }

    // función pura: el resultado depende solo de los argumentos y no toca nada de fuera
    static int sumaPura(int a, int b) {
        return a + b;
    }

    // función impura: lee y modifica una variable externa; el mismo argumento da resultados distintos
    static int total = 0;

    static int sumarAlTotal(int x) {
        total += x;
        return total;
    }

    public static void main(String[] args) {
        Jugador original = new Jugador("Ana", 10);
        Jugador actualizado = original.conPuntos(15);
        System.out.println("original: " + describir(original));
        System.out.println("actualizado: " + describir(actualizado));
        System.out.println("original sigue igual: " + describir(original));

        List<Integer> lista = List.of(1, 2, 3); // add() o remove() lanzarían UnsupportedOperationException
        List<Integer> nueva = Stream.concat(lista.stream(), Stream.of(4)).toList();
        System.out.println("lista original: " + lista);
        System.out.println("lista nueva: " + nueva);

        System.out.println("pura: " + sumaPura(2, 3) + " y " + sumaPura(2, 3));
        System.out.println("impura: " + sumarAlTotal(3) + " y " + sumarAlTotal(3));
    }
}
```

Salida:

```text
original: Ana tiene 10 puntos
actualizado: Ana tiene 15 puntos
original sigue igual: Ana tiene 10 puntos
lista original: [1, 2, 3]
lista nueva: [1, 2, 3, 4]
pura: 5 y 5
impura: 3 y 6
```

Fíjate en las dos últimas líneas: la función **pura** da `5` las dos veces; la **impura** da `3` y luego `6`, porque guarda estado fuera de la función y el mismo argumento produce resultados distintos.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Esperar que una secuencia perezosa «ya esté calculada» | Recuerda que solo se calcula al pedirla; si hay efectos secundarios (como imprimir), salen más tarde |
| Recorrer una secuencia infinita sin límite | Corta siempre con `take`, `limit` o `islice` antes de convertir a lista |
| Modificar una lista que otra parte del programa también usa | Devuelve una copia nueva con el cambio |
| Mezclar funciones puras con efectos secundarios | Separa el cálculo (puro) de lo que imprime o guarda |

## Para practicar

Los ejercicios [A1.5 y A1.6](ejercicios.md) usan una tubería y una secuencia perezosa. Para ver estas ideas en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
