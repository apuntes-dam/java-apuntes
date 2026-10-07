# 4.C Constructores y enumerados

## Más de una forma de crear un objeto

A veces un objeto se puede crear de varias maneras: con unos valores por defecto, con datos propios o a partir de una «receta» ya preparada. Cada lenguaje lo resuelve a su manera.

Java usa la **sobrecarga**: varios constructores (y métodos) con el **mismo nombre y distintos parámetros**, y el compilador elige según los argumentos. Un constructor puede delegar en otro con **`this(...)`** para no repetir código. Java no tiene constructores con nombre ni valores por defecto: se emulan con sobrecarga y con **métodos estáticos de fábrica** (`Pizza.cuatroQuesos(...)`).

```java
import java.util.Arrays;
import java.util.stream.Collectors;

enum Tamano {
    PEQUENA("Pequeña", 6), MEDIANA("Mediana", 8), GRANDE("Grande", 10);

    final String etiqueta;
    final int precioBase;

    Tamano(String etiqueta, int precioBase) {
        this.etiqueta = etiqueta;
        this.precioBase = precioBase;
    }
}

class Pizza {
    private final Tamano tamano;
    private final String nombre;
    private int extras;

    Pizza(Tamano tamano) {
        this(tamano, "Margarita", 0);
    }

    Pizza(Tamano tamano, String nombre) {
        this(tamano, nombre, 0);
    }

    Pizza(Tamano tamano, String nombre, int extras) {
        this.tamano = tamano;
        this.nombre = nombre;
        this.extras = extras;
    }

    static Pizza cuatroQuesos(Tamano tamano) {
        return new Pizza(tamano, "Cuatro quesos", 3);
    }

    void anadirExtra() {
        extras++;
    }

    void anadirExtra(int cantidad) {
        extras += cantidad;
    }

    int precio() {
        return tamano.precioBase + extras;
    }

    @Override
    public String toString() {
        return "Pizza(" + nombre + ", " + tamano.etiqueta + ", " + extras + " extras, " + precio() + " €)";
    }
}

public class Poo3 {
    public static void main(String[] args) {
        Pizza a = new Pizza(Tamano.MEDIANA);
        Pizza b = Pizza.cuatroQuesos(Tamano.GRANDE);
        Pizza c = new Pizza(Tamano.PEQUENA, "Barbacoa");
        System.out.println(a);
        System.out.println(b);
        System.out.println(c);
        a.anadirExtra();
        a.anadirExtra(2);
        System.out.println(a);
        System.out.println("total del pedido: " + (a.precio() + b.precio() + c.precio()) + " €");
        System.out.println("tamaños: " + Arrays.stream(Tamano.values())
                .map(t -> t.etiqueta).collect(Collectors.joining(", ")));
    }
}
```

Salida:

```text
Pizza(Margarita, Mediana, 0 extras, 8 €)
Pizza(Cuatro quesos, Grande, 3 extras, 13 €)
Pizza(Barbacoa, Pequeña, 0 extras, 6 €)
Pizza(Margarita, Mediana, 3 extras, 11 €)
total del pedido: 30 €
tamaños: Pequeña, Mediana, Grande
```

En este ejemplo hay tres formas de obtener una pizza: **solo con el tamaño** (el nombre por defecto es «Margarita»), **con nombre propio** (`Barbacoa`) y la receta ya preparada con un **constructor alternativo** (`cuatroQuesos`, que además trae tres extras). Después, `anadirExtra` se llama **con y sin argumento**.

La **sobrecarga de métodos** es lo mismo que con constructores: `anadirExtra()` (suma uno) y `anadirExtra(int cantidad)` (suma varios) conviven porque se distinguen por sus parámetros.

!!! tip "Un solo sitio para las reglas"
    Procura que **todos los constructores acaben pasando por el mismo código** (un constructor principal al que los demás llaman): así cualquier regla que añadas más adelante (por ejemplo, un máximo de extras) se escribe **una sola vez**.

## Enumerados

Un **enumerado** es un tipo con un **conjunto fijo y cerrado de valores**: los días de la semana, los estados de un pedido, los tamaños de una pizza. Es mejor que usar números o textos sueltos porque el compilador impide valores inventados (`Tamano.gigante` no existe) y el código se lee solo.

Un `enum` en Java es una clase especial: puede tener **campos, constructor (siempre privado) y métodos**. `Tamano.values()` devuelve todos los valores en orden. Por convención las constantes van en MAYÚSCULAS.

En el ejemplo, cada tamaño lleva **datos asociados** (la etiqueta que se muestra y el precio base), de modo que el resto del programa no necesita un `if` por cada tamaño: pregunta el dato al propio valor.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Repetir las comprobaciones en cada constructor | Un constructor principal y los demás que lo llaman |
| Dos constructores casi iguales con distinto orden de parámetros | Usa valores por defecto o un método de fábrica con nombre claro |
| Usar números o textos para representar categorías (`tipo = 1`) | Un enumerado |
| Cadenas de `if`/`else` según el valor de un enumerado | Pon el dato o el comportamiento **dentro** del enumerado |

## Para practicar

Los constructores, los enumerados y la sobrecarga se practican en [U4.6 · Prueba](prueba.md). Compara cómo se escribe en otro lenguaje con [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
