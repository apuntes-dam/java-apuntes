# 3.3 Conjuntos

Un **conjunto** es una colección de elementos **sin repetir**. No se accede por posición: solo interesa **si un elemento está o no**. Eso lo hace muy rápido para comprobar pertenencia y perfecto para eliminar duplicados y comparar grupos.

En Java es `Set<T>`, con tres implementaciones: **`HashSet`** (sin orden), **`LinkedHashSet`** (orden de inserción) y **`TreeSet`** (siempre ordenado, que es la que usa el ejemplo para que la salida sea predecible). Las operaciones de conjuntos modifican el conjunto sobre el que se llaman, por eso se hace una copia antes (`new TreeSet<>(a)`).

## Operaciones

```java
import java.util.List;
import java.util.Set;
import java.util.TreeSet;

public class Set1 {
    public static void main(String[] args) {
        Set<Integer> a = new TreeSet<>(Set.of(1, 2, 3, 4));
        Set<Integer> b = new TreeSet<>(Set.of(3, 4, 5));

        Set<Integer> union = new TreeSet<>(a);
        union.addAll(b);
        Set<Integer> interseccion = new TreeSet<>(a);
        interseccion.retainAll(b);
        Set<Integer> diferencia = new TreeSet<>(a);
        diferencia.removeAll(b);

        System.out.println("unión: " + union);
        System.out.println("intersección: " + interseccion);
        System.out.println("diferencia a - b: " + diferencia);
        System.out.println("¿3 está en a? " + (a.contains(3) ? "sí" : "no"));

        List<Integer> conRepetidos = List.of(3, 1, 3, 2, 1);
        System.out.println("sin repetidos: " + new TreeSet<>(conRepetidos));
        System.out.println("tamaño de a: " + a.size());
    }
}
```

Salida:

```text
unión: [1, 2, 3, 4, 5]
intersección: [3, 4]
diferencia a - b: [1, 2]
¿3 está en a? sí
sin repetidos: [1, 2, 3]
tamaño de a: 4
```

Las tres operaciones clásicas de conjuntos, con `a = {1, 2, 3, 4}` y `b = {3, 4, 5}`:

| Operación | Resultado | Significa |
|---|---|---|
| **Unión** | `{1, 2, 3, 4, 5}` | Lo que está en `a`, en `b` o en los dos |
| **Intersección** | `{3, 4}` | Lo que está en `a` **y** en `b` |
| **Diferencia** `a - b` | `{1, 2}` | Lo que está en `a` pero **no** en `b` |

| Operación | En Java |
|---|---|
| Añadir | `a.add(5)` (devuelve `false` si ya estaba) |
| Quitar | `a.remove(5)` |
| ¿Contiene? | `a.contains(3)` |
| Unión · intersección · diferencia | `addAll(b)` · `retainAll(b)` · `removeAll(b)` |
| Tamaño | `a.size()` |
| Quitar repetidos de una lista | `new LinkedHashSet<>(lista)` |

## Quitar repetidos

Convertir una lista en conjunto elimina los duplicados al instante: `[3, 1, 3, 2, 1]` se queda en `{1, 2, 3}`. Si después necesitas orden, vuelve a convertirlo en lista y ordénalo, como hace el ejemplo.

## ¿Lista o conjunto?

| Necesito... | Estructura |
|---|---|
| Conservar repeticiones y un orden | Lista |
| Saber si un valor está, sin duplicados | **Conjunto** |
| Comparar dos grupos (qué tienen en común) | **Conjunto** |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Esperar un orden concreto al recorrerlo | Ordénalo antes de mostrarlo si importa |
| Intentar acceder por posición (`a[0]`) | Los conjuntos no tienen posiciones: recórrelo |
| Crear un conjunto vacío con `{}` | Es un mapa en Dart y Python: usa `<int>{}` o `set()` |

## Para practicar

Haz los [ejercicios 3.3 de conjuntos](conjuntos.md). Y la comparación entre lenguajes está en [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
