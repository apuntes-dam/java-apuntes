# 5.D Jerarquías cerradas y clases de datos

## El problema: un conjunto de variantes conocido y cerrado

A veces los distintos tipos **no se van a ampliar**: un pago es en efectivo, con tarjeta o por Bizum, y no hay una cuarta posibilidad que inventar desde fuera. Con una jerarquía normal, quien use el tipo base nunca está seguro de haber cubierto todos los casos.

Una **jerarquía cerrada** (*sealed*) le dice al compilador **exactamente cuáles son los subtipos posibles**. Así, cuando se hace algo distinto según el subtipo, el compilador **comprueba que no falte ninguno**: si más adelante se añade un caso nuevo, **avisa en todos los sitios** donde falta tratarlo.

En Java 17+ se declara **`sealed interface Pago permits Efectivo, Tarjeta, Bizum`**: se **listan** las únicas clases permitidas. Se combinan con **`record`** para los datos y con **`switch` con patrones** (`case Tarjeta(int importe, String ultimos4)`), que extrae los datos de cada caso y exige cubrirlos **todos** (los patrones de registros requieren Java 21).

```java
import java.util.List;

sealed interface Pago permits Efectivo, Tarjeta, Bizum {}

record Efectivo(int importe) implements Pago {}

record Tarjeta(int importe, String ultimos4) implements Pago {}

record Bizum(int importe, String telefono) implements Pago {}

public class Sealed1 {
    static String describir(Pago pago) {
        return switch (pago) {
            case Efectivo(int importe) -> "Efectivo de " + importe + " €";
            case Tarjeta(int importe, String ultimos4) -> "Tarjeta ****" + ultimos4 + " de " + importe + " €";
            case Bizum(int importe, String telefono) -> "Bizum al " + telefono + " de " + importe + " €";
        };
    }

    static int comision(Pago pago) {
        return switch (pago) {
            case Efectivo e -> 0;
            case Tarjeta t -> t.importe() * 2 / 100;
            case Bizum b -> 1;
        };
    }

    public static void main(String[] args) {
        List<Pago> pagos = List.of(new Efectivo(50), new Tarjeta(100, "1234"), new Bizum(30, "600123456"));
        int total = 0;
        for (Pago pago : pagos) {
            int c = comision(pago);
            total += c;
            System.out.println(describir(pago) + " -> comisión " + c + " €");
        }
        System.out.println("total de comisiones: " + total + " €");
    }
}
```

Salida:

```text
Efectivo de 50 € -> comisión 0 €
Tarjeta ****1234 de 100 € -> comisión 2 €
Bizum al 600123456 de 30 € -> comisión 1 €
total de comisiones: 3 €
```

Observa:

* Cada variante lleva **datos distintos** (`ultimos4` solo existe en la tarjeta, `telefono` solo en Bizum). Por eso aquí no vale un enumerado.
* Las variantes son **clases de datos**, que solo guardan información y comparan por valor (ver [4.B](../u04/t-encapsulamiento.md)).
* Las funciones `describir` y `comision` **no tienen caso por defecto**: no hace falta, porque están cubiertos todos los posibles.
* El comportamiento **está fuera** de las clases (en las funciones), no dentro de ellas. Se elige así cuando las variantes son solo datos y las operaciones se van añadiendo.

## ¿Enumerado, jerarquía cerrada, abstracta o interfaz?

| Situación | Usa |
|---|---|
| Valores fijos y sencillos, a lo sumo con datos iguales para todos (días, tamaños, estados) | **Enumerado** |
| Variantes cerradas, **cada una con datos distintos** (pagos, resultados, formas de un mensaje) | **Jerarquía cerrada** |
| Familia abierta con **código común** | Clase abstracta |
| Capacidad que clases distintas pueden ofrecer | Interfaz |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Poner un caso `default`/`else` que esconde los casos nuevos | No lo pongas: así el compilador te avisa si falta uno |
| Usar una jerarquía cerrada cuando otras personas deben poder añadir variantes | Interfaz o clase abstracta (jerarquía abierta) |
| Meter la lógica de cada caso en una cadena de `if` y `instanceof` | Un `switch`/`when`/`match` con patrones |
| Usar un enumerado cuando cada variante necesita datos propios | Jerarquía cerrada |

## Para practicar

El ejercicio de la biblioteca de [U5.1](herencia.md) usa clases de datos y una jerarquía cerrada para los usuarios. La comparación entre lenguajes de todos estos tipos de clase está en [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
