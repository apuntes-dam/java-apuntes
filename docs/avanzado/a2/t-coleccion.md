# A2.C Tu colección y qué pasa al ejecutar

## Una colección genérica propia

Las listas, mapas y conjuntos que ya usas son clases genéricas. Aquí escribimos una a mano: una **pila**, donde el último elemento en entrar es el primero en salir (como una pila de platos). La misma clase sirve para letras y para enteros:

```java
import java.util.ArrayList;
import java.util.List;

// Una colección genérica propia: una pila (el último en entrar es el primero en salir).
public class Pila<T> {
    private final List<T> elementos = new ArrayList<>();

    void apilar(T x) {
        elementos.add(x);
    }

    T desapilar() {
        if (elementos.isEmpty()) throw new IllegalStateException("la pila está vacía");
        return elementos.remove(elementos.size() - 1);
    }

    T tope() {
        return elementos.get(elementos.size() - 1);
    }

    int tamano() {
        return elementos.size();
    }

    boolean vacia() {
        return elementos.isEmpty();
    }

    public static void main(String[] args) {
        Pila<String> letras = new Pila<>();
        for (String l : List.of("a", "b", "c")) {
            letras.apilar(l);
        }
        System.out.println("tamaño: " + letras.tamano());
        System.out.println("tope: " + letras.tope());
        System.out.println("sacar: " + letras.desapilar());
        System.out.println("sacar: " + letras.desapilar());
        System.out.println("quedan: " + letras.tamano());
        System.out.println("vacía: " + (letras.vacia() ? "sí" : "no"));
        System.out.println("sacar: " + letras.desapilar());
        try {
            letras.desapilar();
        } catch (IllegalStateException e) {
            System.out.println("error: " + e.getMessage());
        }

        // la misma clase, con otro tipo
        Pila<Integer> numeros = new Pila<>();
        for (int n : List.of(1, 2, 3)) {
            numeros.apilar(n);
        }
        List<Integer> salida = new ArrayList<>();
        while (!numeros.vacia()) {
            salida.add(numeros.desapilar());
        }
        System.out.println("pila de enteros al vaciarla: " + String.join(" ", salida.stream().map(String::valueOf).toList()));
    }
}
```

Salida:

```text
tamaño: 3
tope: c
sacar: c
sacar: b
quedan: 1
vacía: no
sacar: a
error: la pila está vacía
pila de enteros al vaciarla: 3 2 1
```

Qué se ve en la salida:

1. Se apilan `a`, `b`, `c`; el **tope** es la última (`c`) y se sacan en orden contrario: `c`, `b`, `a`.
2. Intentar sacar de una pila **vacía** lanza una excepción con un mensaje claro, que el programa captura. No se devuelve un valor inventado.
3. Con una `Pila<Int>`, los números `1, 2, 3` salen como `3 2 1`: es la misma clase con otro tipo.

!!! tip "Una pila para algo útil"
    Una pila sirve para **deshacer** acciones, comprobar si los paréntesis de una expresión están equilibrados o recorrer estructuras sin recursión. El ejercicio [A2.4](ejercicios.md) la usa para invertir una lista.

## Qué pasa con los tipos al ejecutar

Los genéricos se comprueban **al compilar o al analizar el código**. Lo que ocurre después, al ejecutar, **depende del lenguaje**:

En Java los tipos genéricos **se borran al compilar** (*type erasure*): el compilador comprueba que todo cuadra y después elimina los `<Integer>` y `<String>`. En ejecución solo queda `ArrayList`. Por eso no se puede escribir `x instanceof List<Integer>` ni `new T()`, ni crear un array de `T`: en ejecución, `T` no existe.

```java
import java.util.ArrayList;
import java.util.List;

// En Java los tipos genéricos se BORRAN al compilar (type erasure): al ejecutar solo queda ArrayList.
public class Ejecucion {
    public static void main(String[] args) {
        List<Integer> enteros = new ArrayList<>();
        List<String> textos = new ArrayList<>();

        System.out.println("clase de List<Integer>: " + enteros.getClass().getSimpleName());
        System.out.println("clase de List<String>: " + textos.getClass().getSimpleName());
        System.out.println("¿misma clase en ejecución? " + (enteros.getClass() == textos.getClass() ? "sí" : "no"));
        // enteros instanceof List<Integer> no compila: en ejecución no hay forma de saber que eran enteros
    }
}
```

Salida:

```text
clase de List<Integer>: ArrayList
clase de List<String>: ArrayList
¿misma clase en ejecución? sí
```

Si necesitas el tipo en ejecución, hay que pasarlo a mano: un parámetro `Class<T> tipo`. Así funcionan, por ejemplo, las bibliotecas que leen JSON.

!!! warning "Por eso no se mezclan en la misma lista sin querer"
    En los lenguajes que comprueban al compilar, intentar meter un texto en una lista de enteros ni siquiera compila. En Python, el programa se ejecuta igualmente y el fallo aparece más tarde, cuando otra parte del código espera un número.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Devolver un valor inventado al sacar de una colección vacía | Lanza una excepción con un mensaje claro o devuelve un valor opcional |
| Esperar poder preguntar `¿es una lista de enteros?` al ejecutar en todos los lenguajes | Depende del lenguaje; si lo necesitas, guarda el tipo tú mismo |
| Escribir una colección propia cuando ya existe una en la biblioteca | Usa las del lenguaje salvo que quieras aprender o necesites un comportamiento distinto |

## Para practicar

Los ejercicios [A2.4 y A2.6](ejercicios.md) usan una pila y una caché genéricas. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
