# 3.1 Listas y tuplas

Una **lista** guarda varios valores **ordenados** en una sola variable, y se accede a cada uno por su posición (empezando en 0). Puede crecer y encogerse.

En Java las listas son `List<T>`, y hay que elegir una implementación: **`ArrayList`** (la más usada, modificable). `List.of(...)` crea una lista **inmutable**: si intentas añadir, lanza `UnsupportedOperationException`; por eso el ejemplo hace `new ArrayList<>(List.of(...))`. Con tipos básicos hay que usar la clase envoltorio: `List<Integer>`, no `List<int>`. Los arrays (`int[]`) tienen tamaño fijo.

## Operaciones básicas

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Lis1 {
    public static void main(String[] args) {
        List<Integer> lista = new ArrayList<>(List.of(5, 3, 8, 1));
        System.out.println("lista: " + lista);
        System.out.println("tamaño: " + lista.size() + ", primero: " + lista.get(0)
                + ", último: " + lista.get(lista.size() - 1));

        lista.add(9);
        System.out.println("tras añadir 9: " + lista);
        lista.add(1, 7);
        System.out.println("insertar 7 en la posición 1: " + lista);
        lista.remove(Integer.valueOf(3));
        System.out.println("quitar el valor 3: " + lista);
        lista.remove(0);
        System.out.println("quitar la posición 0: " + lista);
        Collections.sort(lista);
        System.out.println("ordenada: " + lista);

        int suma = 0;
        for (int n : lista) {
            suma += n;
        }
        System.out.println("suma: " + suma + ", mayor: " + Collections.max(lista));
    }
}
```

Salida:

```text
lista: [5, 3, 8, 1]
tamaño: 4, primero: 5, último: 1
tras añadir 9: [5, 3, 8, 1, 9]
insertar 7 en la posición 1: [5, 7, 3, 8, 1, 9]
quitar el valor 3: [5, 7, 8, 1, 9]
quitar la posición 0: [7, 8, 1, 9]
ordenada: [1, 7, 8, 9]
suma: 25, mayor: 9
```

| Operación | En Java |
|---|---|
| Añadir al final | `lista.add(9)` |
| Insertar en una posición | `lista.add(1, 7)` |
| Quitar un **valor** | `lista.remove(Integer.valueOf(3))` |
| Quitar una **posición** | `lista.remove(0)` |
| Ordenar (modifica la lista) | `Collections.sort(lista)` |
| Posición de un valor | `lista.indexOf(8)` (`-1` si no está) |
| ¿Contiene? | `lista.contains(8)` |
| Tamaño · primero · último | `size()` · `get(0)` · `get(size() - 1)` |
| Una parte | `lista.subList(1, 3)` |
| Vaciar | `lista.clear()` |

**Cuidado con `remove`**: en una `List<Integer>`, `lista.remove(0)` quita la **posición** 0, pero para quitar el **valor** 3 hay que escribir `lista.remove(Integer.valueOf(3))`; si escribieras `remove(3)` quitaría la posición 3. Es una trampa clásica de Java.

!!! warning "No cambies una lista mientras la recorres"
    Añadir o quitar elementos dentro del bucle que la recorre da errores o resultados raros. Si quieres filtrar, construye una **lista nueva** con lo que sirve, o usa el método específico de borrado por condición de tu lenguaje.

## Recorrer, copiar y listas anidadas

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Lis2 {
    public static void main(String[] args) {
        List<Integer> numeros = new ArrayList<>(List.of(10, 20, 30));
        List<String> partes = new ArrayList<>();
        for (int i = 0; i < numeros.size(); i++) {
            partes.add(i + ":" + numeros.get(i));
        }
        System.out.println("con índice: " + String.join(" ", partes));

        List<Integer> alias = numeros;
        alias.set(0, 99);
        System.out.println("alias: " + numeros);

        List<Integer> copia = new ArrayList<>(numeros);
        copia.set(0, 1);
        System.out.println("original: " + numeros + ", copia: " + copia);

        int[][] matriz = {{1, 2, 3}, {4, 5, 6}};
        for (int[] fila : matriz) {
            System.out.println("fila: " + Arrays.toString(fila));
        }
        System.out.println("elemento [1][2] = " + matriz[1][2]);
    }
}
```

Salida:

```text
con índice: 0:10 1:20 2:30
alias: [99, 20, 30]
original: [99, 20, 30], copia: [1, 20, 30]
fila: [1, 2, 3]
fila: [4, 5, 6]
elemento [1][2] = 6
```

Hay dos ideas importantes en este ejemplo:

* **Una lista es una referencia.** `alias = numeros` no crea otra lista: las dos variables apuntan a la **misma**, y al cambiar una cambia la otra. Para tener una lista independiente hay que **copiarla**: `new ArrayList<>(numeros)`.
* **La copia es superficial.** Copia los elementos, pero si estos son a su vez listas (como en una matriz), las sublistas se **comparten** entre original y copia.

Una **lista de listas** sirve como tabla o matriz: `matriz[fila][columna]`. Se recorre con dos bucles, uno dentro de otro.

## Tuplas y transformar listas

```java
import java.util.List;

public class Lis3 {
    record Persona(String nombre, int edad) {}

    public static void main(String[] args) {
        Persona persona = new Persona("Ana", 25);
        System.out.println(persona.nombre() + " tiene " + persona.edad() + " años");
        if (persona instanceof Persona(String nombre, int edad)) {
            System.out.println("desempaquetado: " + nombre + ", " + edad);
        }

        List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6);
        List<Integer> cuadrados = numeros.stream()
                .filter(n -> n % 2 == 0)
                .map(n -> n * n)
                .toList();
        System.out.println("cuadrados de los pares: " + cuadrados);
        int suma = cuadrados.stream().mapToInt(Integer::intValue).sum();
        System.out.println("suma de los cuadrados: " + suma);
    }
}
```

Salida:

```text
Ana tiene 25 años
desempaquetado: Ana, 25
cuadrados de los pares: [4, 16, 36]
suma de los cuadrados: 56
```

Java no tiene tuplas; para agrupar valores se usa un **`record`** (Java 16+): `record Persona(String nombre, int edad) {}`. Se accede con `persona.nombre()` y, desde Java 21, se puede **desempaquetar** con `instanceof Persona(String nombre, int edad)`.

En Java se encadenan operaciones con **streams**: `filter` (quedarse con algunos), `map` (transformar) y `toList()`/`sum()` para obtener el resultado.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Acceder a una posición que no existe | Comprueba el tamaño antes: la última posición es el tamaño menos 1 |
| Creer que `b = a` copia la lista | Copia explícitamente cuando necesites una independiente |
| Ordenar una lista y perder el orden original | Ordena una **copia**, o usa la versión que devuelve una lista nueva |
| Modificar la lista mientras se recorre | Construye una lista nueva con el resultado |

## Para practicar

Haz los [ejercicios 3.1 de listas](listas.md). Compara cómo se escribe lo mismo en otro lenguaje en [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
