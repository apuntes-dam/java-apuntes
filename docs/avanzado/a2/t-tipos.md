# A2.A Tipos parametrizados

!!! info "Para quién es esto"
    Es material **avanzado**: da por sabidas las unidades 1 a 5, sobre todo clases e interfaces. Si te haces un lío con los tipos, repasa [las unidades 4 y 5](../../unidades.md).

Una **clase genérica** o una **función genérica** se escribe una sola vez y sirve para **varios tipos**. En lugar de decir «una caja de enteros» y «una caja de textos» con dos clases, dices «una caja de `T`», donde `T` es un **tipo que se decide al usarla**.

## Por qué existe

Sin genéricos habría que guardar el valor como `Object`: cabe cualquier cosa, pero al sacarlo hay que **convertirlo** (`(String) caja.get()`), y si te equivocas, el error aparece al ejecutar (`ClassCastException`). Con `Caja<T>` el compilador recuerda el tipo y el error aparece **al compilar**.

## La sintaxis en Java

| Qué | Cómo se escribe en Java |
|---|---|
| Clase genérica | `class Caja<T> { ... }` |
| Método genérico | `static <T> T primero(List<T> lista)` (el `<T>` va **antes** del tipo de retorno) |
| Usarla | `new Caja<Integer>(42)` o `new Caja<>(42)` (el `<>` deja que deduzca el tipo) |
| Varios parámetros | `record Par<A, B>(A primero, B segundo)` |
| Tipos primitivos | **No valen** como argumento de tipo: `List<int>` no existe, se usa `List<Integer>` |

Por convención, el parámetro de tipo se llama con una letra mayúscula: `T` (*type*), `E` (*element*), `K` y `V` (*key* y *value*), o `A`, `B` cuando hay varios.

## Un ejemplo

Una caja genérica, un par con dos tipos distintos y una función que devuelve el primer elemento de una lista de cualquier tipo:

```java
import java.util.List;

// Tipos genéricos: Caja<T> guarda un valor de cualquier tipo T, y el compilador recuerda cuál es.
public class Caja<T> {
    private final T valor;

    Caja(T valor) {
        this.valor = valor;
    }

    T valor() {
        return valor;
    }

    // Con dos parámetros de tipo: Par<A, B>
    record Par<A, B>(A primero, B segundo) {
        @Override
        public String toString() {
            return "(" + primero + ", " + segundo + ")";
        }
    }

    // Método genérico: sirve para listas de cualquier tipo y devuelve ese mismo tipo
    static <T> T primero(List<T> lista) {
        return lista.get(0);
    }

    public static void main(String[] args) {
        Caja<String> texto = new Caja<>("hola"); // el <> deja que el compilador deduzca el tipo
        Caja<Integer> numero = new Caja<>(42);
        System.out.println("caja de texto: " + texto.valor());
        System.out.println("caja de entero: " + numero.valor());
        System.out.println("par: " + new Par<>("Ana", 30));
        System.out.println("primero de [3, 4, 5]: " + primero(List.of(3, 4, 5)));
        System.out.println("primero de [a, b]: " + primero(List.of("a", "b")));

        // texto.valor() es un String de verdad: int n = texto.valor(); no compilaría
        String t = texto.valor();
        System.out.println("longitud: " + t.length());
    }
}
```

Salida:

```text
caja de texto: hola
caja de entero: 42
par: (Ana, 30)
primero de [3, 4, 5]: 3
primero de [a, b]: a
longitud: 4
```

Fíjate en lo que **no** hace falta: ni convertir tipos ni escribir una clase por cada tipo. La función `primero` devuelve un entero cuando le das enteros y un texto cuando le das textos, y el lenguaje lo sabe.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Usar `Object`/`Any`/`dynamic` «para que valga con todo» | Si lo único que cambia es el tipo, usa un parámetro de tipo |
| Escribir `T` y luego querer llamar a un método suyo | Sin restricción, de `T` no se sabe nada: mira [A2.B](t-restricciones.md) |
| Creer que un genérico existe igual al ejecutar en todos los lenguajes | No es así: mira [A2.C](t-coleccion.md) |

## Para practicar

Los ejercicios [A2.1 y A2.2](ejercicios.md) usan una función y una clase genéricas. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
