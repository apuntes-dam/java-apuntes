# A4.A Qué es una prueba y cómo se escribe

!!! info "Para quién es esto"
    Es material **avanzado**: da por sabidas las unidades 1 a 5. La [unidad 1](../../u01/04-pruebas.md) ya te enseñó una primera prueba; aquí aprendes a escribirlas con método.

Una **prueba automática** es un programa corto que ejecuta tu código con unos datos **y comprueba el resultado por ti**. Lo que compruebas a mano una vez, la prueba lo comprueba **todas las veces**, en segundos, cada vez que cambias algo. Si rompes algo sin querer, lo sabes **al momento** y no dentro de una semana.

## La receta: preparar, actuar, comprobar

Casi todas las pruebas siguen tres pasos (*Arrange, Act, Assert*):

| Paso | Qué haces | En el ejemplo |
|---|---|---|
| **Preparar** | Creas los objetos y los datos | Un carrito nuevo |
| **Actuar** | Llamas al código que se prueba | `agregar('tarta', 1800, 2)` |
| **Comprobar** | Dices qué esperas | El total es `3600` |

Una buena prueba **comprueba una sola cosa**, tiene un **nombre que dice lo que debe ocurrir** y **no depende de otra prueba**.

## El marco de pruebas de Java

Esta página usa **JUnit 4** (`junit:junit:4.13.2`). Cada método `public` con **`@Test`** dentro de una clase `public` es una prueba y las comprobaciones son los métodos de `org.junit.Assert` (`assertEquals`, `assertTrue`...). Se ejecutan desde el IDE, con Maven (`mvn test`) o con Gradle (`gradle test`). Los resultados de abajo vienen de lanzar **`org.junit.runner.JUnitCore`** a mano.

## JUnit 4 y JUnit 5

La [unidad 1](../../u01/04-pruebas.md) explica **JUnit 5**; esta unidad usa **JUnit 4** porque es el que he podido ejecutar y comprobar. La idea es idéntica y casi todo cambia solo de nombre:

| | JUnit 4 (esta unidad) | JUnit 5 (unidad 1) |
|---|---|---|
| Dependencia | `junit:junit:4.13.2` | `org.junit.jupiter:junit-jupiter` |
| Importar `@Test` | `org.junit.Test` | `org.junit.jupiter.api.Test` |
| Comprobaciones | `org.junit.Assert.*` | `org.junit.jupiter.api.Assertions.*` |
| Antes de cada prueba | `@Before` | `@BeforeEach` |
| Clase y métodos de prueba | Tienen que ser **`public`** | Pueden ser visibles solo en el paquete |
| Mensaje de un `assertEquals` | **Primer** parámetro | **Último** parámetro |
| Probar una excepción | `assertThrows` (desde la 4.13) | `assertThrows` |

Si tu proyecto usa JUnit 5, copia los ejemplos cambiando solo esas líneas.

## Un ejemplo: un carrito de la compra

Primero, el código que se prueba. Los precios van en **céntimos** (enteros) para evitar errores de decimales:

**`Carrito.java`**

```java
import java.util.ArrayList;
import java.util.List;

// El código que se prueba: un carrito de la compra.
// Los precios van en céntimos (números enteros) para evitar errores de redondeo con decimales.
public class Carrito {
    private record Linea(String producto, int precio, int cantidad) {}

    private final List<Linea> lineas = new ArrayList<>();

    public void agregar(String producto, int precio, int cantidad) {
        if (cantidad <= 0) throw new IllegalArgumentException("la cantidad debe ser mayor que cero");
        if (precio < 0) throw new IllegalArgumentException("el precio no puede ser negativo");
        lineas.add(new Linea(producto, precio, cantidad));
    }

    public int total() {
        return lineas.stream().mapToInt(l -> l.precio() * l.cantidad()).sum();
    }

    public int unidades() {
        return lineas.stream().mapToInt(Linea::cantidad).sum();
    }

    public boolean vacio() {
        return lineas.isEmpty();
    }

    /** El total después de aplicar un descuento de 0 a 100 por ciento. */
    public int conDescuento(int porcentaje) {
        if (porcentaje < 0 || porcentaje > 100) {
            throw new IllegalArgumentException("el porcentaje debe estar entre 0 y 100");
        }
        return total() - total() * porcentaje / 100;
    }
}
```

Y sus tres primeras pruebas:

**`CarritoTest.java`**

```java
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertTrue;

import org.junit.Before;
import org.junit.Test;

// JUnit 4: cada método con @Test es una prueba; @Before se ejecuta antes de CADA una.
public class CarritoTest {
    private Carrito carrito;

    @Before
    public void preparar() {
        carrito = new Carrito(); // un carrito nuevo antes de CADA prueba: así no dependen unas de otras
    }

    @Test
    public void unCarritoNuevoEstaVacioYSuTotalEs0() {
        assertTrue(carrito.vacio());
        assertEquals(0, carrito.total());
    }

    @Test
    public void agregarUnProductoSumaSuImporte() {
        carrito.agregar("tarta", 1800, 2);
        assertEquals(3600, carrito.total());
        assertEquals(2, carrito.unidades());
    }

    @Test
    public void conVariosProductosElTotalEsLaSumaDeLosImportes() {
        carrito.agregar("tarta", 1800, 2);
        carrito.agregar("galleta", 100, 12);
        assertEquals(4800, carrito.total());
    }
}
```

El método con **`@Before`** se ejecuta **antes de cada prueba**. Por eso el carrito se vuelve a crear: ninguna prueba hereda lo que hizo la anterior.

Al ejecutar las pruebas:

```text
...
OK (3 tests)
```

Cada **punto** es una prueba que pasó. `OK (3 tests)` significa que se ejecutaron tres y las tres pasaron.

## Cuando una prueba falla

Para ver un fallo **de verdad**, rompí el código a propósito: cambié `precio × cantidad` por `precio + cantidad` en el cálculo del total. Esto es lo que se ve:

```text
1) agregarUnProductoSumaSuImporte(CarritoTest)
java.lang.AssertionError: expected:<3600> but was:<1802>
	at CarritoTest.agregarUnProductoSumaSuImporte(CarritoTest.java:25)
```

Un buen mensaje de fallo dice **qué prueba falló**, **qué se esperaba** (`3600`) y **qué se obtuvo** (`1802`). Con eso suele bastar para encontrar el error sin depurar.

!!! tip "Una prueba que nunca ha fallado no demuestra nada"
    Cuando escribas una prueba, **rómpela una vez a propósito** (cambia el resultado esperado o el código) y comprueba que falla. Así sabes que de verdad está mirando algo.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Pruebas que dependen unas de otras (el orden importa) | Prepara de cero antes de cada prueba |
| Nombres como `test1`, `test2` | Describe el comportamiento: «agregar un producto suma su importe» |
| Comprobar muchas cosas distintas en una sola prueba | Una idea por prueba: si falla, sabes qué falló |
| Copiar en la prueba el mismo cálculo que hace el código | Escribe el resultado esperado **a mano** (`3600`), no `precio * cantidad` |

## Para practicar

Los ejercicios [A4.1 y A4.4](ejercicios.md) piden escribir una función con sus pruebas y una preparación previa. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
