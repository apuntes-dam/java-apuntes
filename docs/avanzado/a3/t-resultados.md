# A3.B Resultados, errores y esperas

Lanzar una tarea es solo la mitad: casi siempre querrás **recoger su resultado** y saber si **falló**. Lo que representa «un resultado que todavía no existe» recibe distintos nombres según el lenguaje, pero la idea es la misma.

## Resultados futuros en Java

`CompletableFuture<T>` representa un resultado futuro. `supplyAsync(funcion)` lo calcula en otro hilo; `join()` (o `get()`) **espera** y devuelve el valor. Si la tarea falla, `join()` lanza una **`CompletionException`** (y `get()` una `ExecutionException`) que **envuelve** la excepción real: se obtiene con `getCause()`. Para limitar la espera: `get(tiempo, unidad)`, que lanza `TimeoutException`, o `orTimeout(...)`.

| Qué | En Java |
|---|---|
| Un valor que llegará | `CompletableFuture<T>` / `Future<T>` |
| Esperar el valor | `f.join()` / `f.get()` |
| Lanzar varias y esperar todas | `CompletableFuture.allOf` |
| Límite de tiempo | `get(t, unidad)` |

## Un ejemplo: tres consultas y un error

Se consultan los precios de tres productos **a la vez** (cada consulta tarda 100 ms), se suman, y después se pide un producto que no existe para ver cómo llega el error:

```java
import java.util.List;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.CompletionException;

// Tareas que DEVUELVEN un resultado (o un error): se lanzan todas a la vez y se recogen con join().
public class Resultados {
    static final Map<String, Integer> PRECIOS = Map.of("pan", 2, "leche", 1, "queso", 5);

    static int precio(String producto) {
        try {
            Thread.sleep(100); // simula una consulta lenta
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        Integer p = PRECIOS.get(producto);
        if (p == null) throw new IllegalArgumentException("producto desconocido: " + producto);
        return p;
    }

    public static void main(String[] args) {
        List<String> productos = List.of("pan", "leche", "queso");

        // supplyAsync ejecuta la función en otro hilo y devuelve un CompletableFuture con el resultado futuro
        List<CompletableFuture<Integer>> consultas = productos.stream()
                .map(p -> CompletableFuture.supplyAsync(() -> precio(p))) // las tres avanzan a la vez
                .toList();

        int total = 0;
        for (int i = 0; i < productos.size(); i++) {
            int p = consultas.get(i).join(); // espera ese resultado
            System.out.println("precio de " + productos.get(i) + ": " + p);
            total += p;
        }
        System.out.println("total: " + total);

        // un error dentro de la tarea llega envuelto en una CompletionException; la causa es la excepción real
        try {
            CompletableFuture.supplyAsync(() -> precio("caviar")).join();
        } catch (CompletionException e) {
            System.out.println("error: " + e.getCause().getMessage());
        }
    }
}
```

Salida:

```text
precio de pan: 2
precio de leche: 1
precio de queso: 5
total: 8
error: producto desconocido: caviar
```

Cuidado con la envoltura: se captura `CompletionException` y el mensaje original está en `e.getCause().getMessage()`.

Los resultados se muestran **en el orden en que se pidieron**, aunque las consultas hayan terminado en otro. Es lo que se espera de una lista de resultados.

!!! tip "Esperar no es lo mismo que bloquear"
    `await`, `join` o `get` **esperan** al resultado. En los modelos con `await` (Dart, Kotlin, Python) esa espera deja libre al hilo para otras tareas. En `join()`/`get()` de Java **el hilo que espera se queda parado**: es lo normal en un programa de consola, pero no en una interfaz gráfica, que se congelaría.

## Límites de tiempo y cancelación

Una tarea que tarda demasiado puede dejar tu programa colgado. Todo lenguaje ofrece una **espera con límite** (mira la tabla de arriba): si se pasa el tiempo, obtienes un error o un valor vacío y decides qué hacer, por ejemplo **reintentar** o avisar. Cancelar la tarea libera recursos, pero en muchos casos solo se **solicita** y la tarea tiene que cooperar.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Lanzar varias tareas y esperarlas **una por una al lanzarlas** (cada una espera a la anterior) | Lánzalas todas primero y luego espera: así avanzan a la vez |
| Olvidar capturar el error de una tarea | El error llega al esperar el resultado: ahí va el `try` |
| Esperar sin límite una operación de red | Pon un límite de tiempo |
| Reintentar sin pausa ni tope | Limita el número de intentos (ejercicio [A3.4](ejercicios.md)) y espera entre ellos |

## Para practicar

Los ejercicios [A3.3 y A3.4](ejercicios.md) usan un límite de tiempo y reintentos. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
