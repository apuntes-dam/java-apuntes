# 4.B Encapsulamiento

**Encapsular** es **ocultar el estado interno** de un objeto y dejar solo unas operaciones controladas para usarlo. Así el objeto se encarga de que sus datos siempre tengan sentido: un precio nunca es negativo, un stock nunca baja de cero.

Sin encapsulamiento, cualquiera podría escribir `producto.precio = -5` y el error aparecería mucho más tarde, lejos de donde se produjo. Con él, el fallo salta **justo al intentar el cambio incorrecto**.

## Visibilidad

Java tiene `private` (solo la propia clase), `protected` (la clase, sus subclases y su paquete), `public` (todos) y, sin nada, visibilidad de **paquete**. Lo habitual es que los **atributos sean `private`** y los métodos que usan otros, `public`.

## Acceso controlado y validación

```java
class Producto {
    private final String nombre;
    private int precio;
    private int stock;

    Producto(String nombre, int precio, int stock) {
        comprobarPrecio(precio);
        this.nombre = nombre;
        this.precio = precio;
        this.stock = stock;
    }

    String getNombre() {
        return nombre;
    }

    int getPrecio() {
        return precio;
    }

    int getStock() {
        return stock;
    }

    int getValorStock() {
        return precio * stock;
    }

    void setPrecio(int nuevo) {
        comprobarPrecio(nuevo);
        this.precio = nuevo;
    }

    void vender(int cantidad) {
        if (cantidad > stock) {
            throw new IllegalStateException("no hay stock suficiente");
        }
        stock -= cantidad;
    }

    private static void comprobarPrecio(int precio) {
        if (precio < 0) {
            throw new IllegalArgumentException("el precio no puede ser negativo");
        }
    }
}

public class Poo2 {
    public static void main(String[] args) {
        Producto p = new Producto("Cuaderno", 3, 10);
        System.out.println(p.getNombre() + ": " + p.getPrecio() + " € x " + p.getStock() + " = " + p.getValorStock() + " €");
        p.vender(4);
        System.out.println("tras vender 4, quedan " + p.getStock());
        try {
            p.vender(20);
        } catch (IllegalStateException e) {
            System.out.println("Error: " + e.getMessage());
        }
        try {
            p.setPrecio(-1);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
        p.setPrecio(7);
        System.out.println("nuevo valor del stock: " + p.getValorStock() + " €");
        try {
            new Producto("Roto", -2, 1);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

Salida:

```text
Cuaderno: 3 € x 10 = 30 €
tras vender 4, quedan 6
Error: no hay stock suficiente
Error: el precio no puede ser negativo
nuevo valor del stock: 42 €
Error: el precio no puede ser negativo
```

Qué hace cada pieza:

* **Atributos protegidos** (`precio`, `stock`): no se pueden cambiar desde fuera sin pasar por el código de la clase.
* **Validar al crear y al modificar**: el constructor y el cambio de precio comprueban el valor y **lanzan una excepción** si no es válido (ver [2.3 Excepciones](../u02/02-excepciones.md)).
* **Solo lectura**: el stock se puede consultar, pero **solo** `vender` lo modifica.
* **Propiedad calculada**: `valorStock` no se guarda; se calcula cada vez a partir de `precio` y `stock`, así nunca queda desactualizada.

Se escriben métodos **`getX()`** y **`setX(...)`** (*getters* y *setters*). Un atributo de solo lectura tiene `get` pero no `set`. Una propiedad calculada, como `getValorStock()`, es un método sin atributo detrás.

!!! tip "Valida antes de modificar"
    Fíjate en `vender`: primero comprueba que hay stock y **solo después** resta. Si la comprobación falla, el objeto queda como estaba. Modificar primero y validar después deja objetos a medias.

## Igualdad: identidad frente a valor

Dos objetos pueden ser **el mismo** (la misma referencia) o ser **iguales** (tener el mismo contenido). Por defecto, `==` solo reconoce lo primero; para que dos puntos con las mismas coordenadas sean iguales hay que decírselo.

```java
import java.util.HashSet;
import java.util.Set;

class PuntoSimple {
    final int x, y;

    PuntoSimple(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

record Punto(int x, int y) {}

public class Poo6 {
    public static void main(String[] args) {
        System.out.println("sin igualdad por valor: " + (new PuntoSimple(1, 2).equals(new PuntoSimple(1, 2)) ? "sí" : "no"));
        System.out.println("con igualdad por valor: " + (new Punto(1, 2).equals(new Punto(1, 2)) ? "sí" : "no"));
        Set<Punto> conjunto = new HashSet<>();
        conjunto.add(new Punto(1, 2));
        conjunto.add(new Punto(1, 2));
        conjunto.add(new Punto(3, 4));
        System.out.println("puntos distintos en el conjunto: " + conjunto.size());
    }
}
```

Salida:

```text
sin igualdad por valor: no
con igualdad por valor: sí
puntos distintos en el conjunto: 2
```

Un **`record`** (Java 16+) genera solo `equals`, `hashCode` y `toString`, comparando todos sus campos, y es inmutable. En una clase normal habría que escribir `equals` y `hashCode` a mano.

Por la misma razón, en un **conjunto** o como **clave de un mapa**, la igualdad por valor decide si dos objetos son «el mismo elemento» (ver [3.3 Conjuntos](../u03/03-conjuntos.md)).

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Atributos públicos y modificables desde cualquier sitio | Hazlos privados y ofrece métodos para lo que haga falta |
| Un *setter* que acepta cualquier valor | Valida y lanza una excepción si no es correcto |
| Ofrecer un *setter* para todo «por si acaso» | Solo lo que de verdad deba poder cambiar: lo demás, de solo lectura |
| Validar en el constructor pero no en el *setter* (o al revés) | El mismo control en **todas** las puertas de entrada |
| Sobrescribir `equals` sin `hashCode` (o al revés) | Siempre juntos y con los mismos campos |

## Para practicar

Los ejercicios de validación y atributos de solo lectura están en [U4.2 · POO I](poo-1.md). Para ver cómo se escribe en otro lenguaje: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
