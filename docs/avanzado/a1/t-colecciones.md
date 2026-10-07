# A1.B Colecciones sin bucles

Casi todo lo que haces con una lista con un `for` entra en unas pocas operaciones: **quedarse con** unos elementos, **transformarlos**, **reducirlos** a un valor, **ordenarlos**, **agruparlos** y **preguntar** si alguno o todos cumplen algo. Si las pasas como funciones, el programa dice **qué** quieres, no **cómo** recorrerlo.

## Las operaciones en Java

| Qué quieres | Cómo se hace |
|---|---|
| Quedarse con los que cumplen | `filter(p -> ...)` |
| Transformar cada elemento | `map(p -> ...)` |
| Reducir a un solo valor | `reduce(...)`, o `sum()` y `count()` en los *streams* de primitivos |
| Ordenar con un criterio | `sorted(Comparator...)` (devuelve otro *stream*) |
| Agrupar o contar por clave | `collect(Collectors.groupingBy(...))` |
| ¿Alguno / todos? | `anyMatch(...)` / `allMatch(...)` |

## Un ejemplo completo

Una pastelería guarda sus pedidos (producto, cantidad y precio por unidad). Con las operaciones anteriores se responde a todo sin un solo bucle explícito:

```java
import java.util.Comparator;
import java.util.List;
import java.util.TreeMap;
import java.util.stream.Collectors;

// Pedidos de una pastelería: filtrar, transformar, reducir, ordenar y agrupar sin escribir bucles.
public class Colecciones {
    record Pedido(String producto, int cantidad, int precio) {
        int importe() {
            return cantidad * precio;
        }
    }

    public static void main(String[] args) {
        List<Pedido> pedidos = List.of(
                new Pedido("tarta", 2, 18),
                new Pedido("galleta", 12, 1),
                new Pedido("tarta", 1, 18),
                new Pedido("pan", 3, 2),
                new Pedido("galleta", 7, 1));

        // filter + map: los productos de los pedidos con 3 o más unidades
        String grandes = pedidos.stream()
                .filter(p -> p.cantidad() >= 3)
                .map(Pedido::producto)
                .collect(Collectors.joining(", "));
        System.out.println("grandes: " + grandes);

        // map: de pedidos a importes
        List<Integer> importes = pedidos.stream().map(Pedido::importe).toList();
        System.out.println("importes: " + importes);

        // reduce / sum: de muchos valores a uno
        System.out.println("total: " + pedidos.stream().mapToInt(Pedido::importe).sum());

        // sorted con un criterio (no modifica la lista original)
        String ordenados = pedidos.stream()
                .sorted(Comparator.comparingInt(Pedido::importe).reversed())
                .map(p -> p.producto() + " " + p.importe())
                .collect(Collectors.joining(", "));
        System.out.println("mayor a menor: " + ordenados);

        // agrupar: unidades por producto (TreeMap para que salga ordenado por clave)
        var porProducto = pedidos.stream().collect(Collectors.groupingBy(
                Pedido::producto, TreeMap::new, Collectors.summingInt(Pedido::cantidad)));
        String unidades = porProducto.entrySet().stream()
                .map(e -> e.getKey() + "=" + e.getValue())
                .collect(Collectors.joining(", "));
        System.out.println("unidades: " + unidades);

        // anyMatch / allMatch: preguntas sobre toda la colección
        System.out.println("¿alguno vale más de 30? " + (pedidos.stream().anyMatch(p -> p.importe() > 30) ? "sí" : "no"));
        System.out.println("¿todos tienen cantidad > 0? " + (pedidos.stream().allMatch(p -> p.cantidad() > 0) ? "sí" : "no"));
    }
}
```

Salida:

```text
grandes: galleta, pan, galleta
importes: [36, 12, 18, 6, 7]
total: 79
mayor a menor: tarta 36, tarta 18, galleta 12, galleta 7, pan 6
unidades: galleta=19, pan=3, tarta=3
¿alguno vale más de 30? sí
¿todos tienen cantidad > 0? sí
```

Cada línea de la salida es una de las operaciones de la tabla. Compruébalas a mano: los importes son `2·18`, `12·1`, `1·18`, `3·2` y `7·1`; el total es `79`; y hay `19` galletas porque `12 + 7`.

## Lo que conviene saber

Un *stream* **no guarda datos**: describe un recorrido sobre una colección. Se puede usar **una sola vez** (volver a usarlo lanza `IllegalStateException`), no modifica la colección original y no hace nada hasta que se llama a una operación **terminal** (`toList()`, `collect`, `sum`, `anyMatch`...).

!!! warning "Un orden estable"
    Si dos elementos empatan en el criterio de orden, no des por hecho en qué orden saldrán. Los datos del ejemplo no tienen empates a propósito. Si te hacen falta, añade un segundo criterio.

!!! tip "¿Bucle o función?"
    No es mejor siempre una cosa que otra. Un bucle sigue siendo lo más claro cuando el cuerpo hace varias cosas distintas o hay que parar a mitad. Usa las operaciones de colección cuando el trabajo es **una transformación de datos** y verás que el código se lee casi como la descripción del problema.

## Para practicar

Los ejercicios [A1.3 y A1.4](ejercicios.md) usan estas operaciones. Para ver la misma tabla en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
