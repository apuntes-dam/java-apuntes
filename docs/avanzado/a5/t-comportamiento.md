# A5.B Patrones de comportamiento

Los patrones **de comportamiento** tratan de **cómo colaboran los objetos** y de cómo se reparten el trabajo.

## Estrategia: cambiar el algoritmo

La **estrategia** saca un algoritmo fuera de una clase para poder **cambiarlo sin tocarla**. Aquí, `Caja` no sabe cómo se calcula un descuento: recibe una estrategia, y se le puede dar «sin descuento», «diez por ciento» o «dos por uno».

En Java la estrategia es una **interfaz con un solo método** (`Descuento`), así que cada estrategia puede ser una **referencia a método** (`Estrategia::dosPorUno`) o una lambda. Antes de Java 8 había que escribir una clase por estrategia.

```java
import java.util.Comparator;
import java.util.List;

// Estrategia: el algoritmo (cómo se calcula el descuento) se pasa como parámetro y se puede cambiar.
// Como Descuento tiene un solo método, una estrategia puede ser una lambda o una referencia a método.
public class Estrategia {
    @FunctionalInterface
    interface Descuento {
        int aplicar(List<Integer> precios);
    }

    static int sinDescuento(List<Integer> precios) {
        return precios.stream().mapToInt(Integer::intValue).sum();
    }

    static int diezPorCiento(List<Integer> precios) {
        return sinDescuento(precios) * 90 / 100;
    }

    static int dosPorUno(List<Integer> precios) {
        List<Integer> orden = precios.stream().sorted(Comparator.reverseOrder()).toList(); // de más caro a más barato
        int total = 0;
        for (int i = 0; i < orden.size(); i += 2) {
            total += orden.get(i); // de cada pareja se paga la más cara
        }
        return total;
    }

    static final class Caja {
        private final Descuento descuento;

        Caja(Descuento descuento) {
            this.descuento = descuento;
        }

        int cobrar(List<Integer> precios) {
            return descuento.aplicar(precios);
        }
    }

    public static void main(String[] args) {
        List<Integer> precios = List.of(300, 450, 150, 100);
        System.out.println("sin descuento: " + new Caja(Estrategia::sinDescuento).cobrar(precios));
        System.out.println("diez por ciento: " + new Caja(Estrategia::diezPorCiento).cobrar(precios));
        System.out.println("dos por uno: " + new Caja(Estrategia::dosPorUno).cobrar(precios));
    }
}
```

Salida:

```text
sin descuento: 1000
diez por ciento: 900
dos por uno: 600
```

Los tres resultados salen de **la misma caja** con **distinta estrategia**. Añadir un descuento nuevo es escribir una función nueva: no se toca `Caja` (es la «O» de SOLID, *abierto para ampliar y cerrado para modificar*). Es el patrón que más se parece a lo que ya viste en [A1](../a1/t-funciones.md): **pasar una función como parámetro**.

## Observador: avisar sin conocer

Un **observador** deja que un objeto **avise a otros** cuando le pasa algo, **sin saber quiénes son ni cuántos**. Quien quiere enterarse se **suscribe**, y puede **darse de baja**.

Los oyentes son `Consumer<String>` guardados en una lista, y `suscribir` devuelve un `Runnable` que sirve para darse de baja. Java trae una versión clásica en `java.beans` (`PropertyChangeListener`), y en los programas con interfaz gráfica los *listeners* de botones y ventanas son este patrón.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;

// Observador: un objeto avisa a todos los que se han suscrito cuando ocurre algo, sin saber quiénes son.
public class Observador {
    static final class Cafetera {
        private final List<Consumer<String>> oyentes = new ArrayList<>();

        // suscribir devuelve un Runnable para darse de baja
        Runnable suscribir(Consumer<String> oyente) {
            oyentes.add(oyente);
            return () -> oyentes.remove(oyente);
        }

        void preparar() {
            for (Consumer<String> oyente : new ArrayList<>(oyentes)) {
                // se recorre una copia por si alguien se da de baja mientras se avisa
                oyente.accept("café listo");
            }
        }
    }

    public static void main(String[] args) {
        Cafetera cafetera = new Cafetera();
        cafetera.suscribir(mensaje -> System.out.println("pantalla: " + mensaje));
        Runnable bajaMovil = cafetera.suscribir(mensaje -> System.out.println("móvil: " + mensaje));

        cafetera.preparar();
        bajaMovil.run();
        System.out.println("(el móvil se da de baja)");
        cafetera.preparar();
    }
}
```

Salida:

```text
pantalla: café listo
móvil: café listo
(el móvil se da de baja)
pantalla: café listo
```

La cafetera no conoce a la pantalla ni al móvil: solo sabe que tiene una lista de oyentes. Después de la baja, el móvil **deja de recibir** avisos y la pantalla sigue recibiéndolos.

!!! warning "Darse de baja es obligatorio"
    Un oyente que no se da de baja **sigue vivo**, aunque ya no se use: consume memoria y puede ejecutar código en un momento en que no debe. Es una fuente clásica de fallos en interfaces gráficas y en Android. Y fíjate en que el ejemplo **recorre una copia** de la lista al avisar: si un oyente se diera de baja *durante* el aviso, modificar la lista que se está recorriendo daría error.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Una cadena de `if` para elegir el algoritmo | Pasa el algoritmo como estrategia |
| Crear una clase por estrategia cuando basta una función | Si la estrategia no tiene estado, usa una función |
| Suscribirse y no darse nunca de baja | Guarda la función de baja y llámala al terminar |
| Que un oyente lento bloquee a los demás | Avisa rápido; el trabajo largo, en otra tarea ([A3](../a3/t-tareas.md)) |
| Depender del orden en que se avisa a los oyentes | No se garantiza en general: no escribas código que lo necesite |

## Para practicar

Los ejercicios [A5.4 y A5.5](ejercicios.md) piden ordenar con estrategias y un termómetro observable. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
