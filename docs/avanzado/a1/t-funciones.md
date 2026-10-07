# A1.A Funciones como valores

!!! info "Para quién es esto"
    Es material **avanzado**: da por sabidas las unidades 1 a 5 (variables, estructuras de control, colecciones y clases). Si alguna idea se te resiste, vuelve a la unidad correspondiente.

Hasta ahora has **llamado** funciones. Aquí das un paso más: tratar una función **como un valor**. Se puede guardar en una variable, pasar a otra función y devolver desde una función. Es la base de cosas que ya usas sin darte cuenta: ordenar con un criterio, reaccionar a un clic o filtrar una lista.

## Tipo de una función en Java

Java no tiene un «tipo función» propio: usa **interfaces funcionales**, interfaces con un único método abstracto. Las del paquete `java.util.function` cubren casi todo: `Function<T,R>` (recibe T, devuelve R), `Predicate<T>` (devuelve `boolean`), `Supplier<T>` (no recibe, devuelve T), `Consumer<T>` (recibe, no devuelve) y variantes para tipos primitivos como `IntUnaryOperator`, que evitan convertir a `Integer`. Una **lambda** `n -> n * 2` es una forma corta de implementar esa interfaz, y una **referencia a método** `Lambdas::doble` apunta a un método que ya existe.

## Un ejemplo con todo

El programa define una función, la pasa a otra, devuelve una función desde una función y crea dos contadores independientes:

```java
import java.util.function.IntSupplier;
import java.util.function.IntUnaryOperator;

public class Lambdas {
    static int doble(int n) {
        return n * 2;
    }

    // Recibe una función como parámetro
    static int aplicarDosVeces(IntUnaryOperator f, int x) {
        return f.applyAsInt(f.applyAsInt(x));
    }

    // Devuelve una función que "recuerda" factor
    static IntUnaryOperator multiplicador(int factor) {
        return n -> n * factor;
    }

    // Una lambda solo captura variables que no cambian; para llevar la cuenta se usa un objeto mutable (aquí, un array)
    static IntSupplier crearContador() {
        int[] cuenta = {0};
        return () -> ++cuenta[0];
    }

    public static void main(String[] args) {
        IntUnaryOperator cuadrado = n -> n * n; // función anónima guardada en una variable

        System.out.println("doble(4) = " + doble(4));
        System.out.println("cuadrado(4) = " + cuadrado.applyAsInt(4));
        System.out.println("aplicar dos veces doble a 3 = " + aplicarDosVeces(Lambdas::doble, 3));
        System.out.println("multiplicador(5)(7) = " + multiplicador(5).applyAsInt(7));

        IntSupplier a = crearContador();
        IntSupplier b = crearContador();
        System.out.println("contador A: " + a.getAsInt() + " " + a.getAsInt() + " " + a.getAsInt());
        System.out.println("contador B: " + b.getAsInt());
    }
}
```

Salida:

```text
doble(4) = 8
cuadrado(4) = 16
aplicar dos veces doble a 3 = 12
multiplicador(5)(7) = 35
contador A: 1 2 3
contador B: 1
```

Qué ocurre en cada línea:

| Línea de salida | Qué demuestra |
|---|---|
| `doble(4)` y `cuadrado(4)` | Una función con nombre y otra **anónima guardada en una variable** se llaman igual |
| `aplicar dos veces doble a 3` | Una función recibe **otra función como parámetro** y la usa dos veces |
| `multiplicador(5)(7)` | Una función **devuelve otra función**, que recuerda el factor `5` |
| `contador A` y `contador B` | Cada contador **recuerda su propia cuenta**: no se pisan |

## Cierres: funciones que recuerdan

Una lambda también puede **capturar** variables del método que la rodea, pero con una restricción: esas variables tienen que ser **efectivamente finales** (no cambiar después de asignarse). Por eso, para llevar una cuenta que cambia, el ejemplo guarda el valor dentro de un array de un elemento: la *referencia* al array no cambia, aunque su contenido sí.

!!! tip "Cuándo usarlo"
    Si necesitas «una operación que se decide más tarde» (un criterio de orden, un filtro, qué hacer al terminar), pásala como función. Es más corto y más flexible que crear una clase solo para eso.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Llamar a la función al pasarla (`doble(3)` en vez de `doble`) | Pasa el nombre **sin paréntesis** si quieres pasar la función, no su resultado |
| Esperar que cada llamada comparta o no comparta estado sin comprobarlo | Crea el cierre una vez por contador, como en el ejemplo |
| Escribir lambdas largas e ilegibles | Si pasa de una o dos líneas, ponle nombre a la función |

## Para practicar

Los ejercicios [A1.1 y A1.2](ejercicios.md) usan estas ideas. Para ver la misma idea escrita en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
