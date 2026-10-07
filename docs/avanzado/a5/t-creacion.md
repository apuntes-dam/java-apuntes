# A5.A Patrones de creación

!!! info "Para quién es esto"
    Es material **avanzado**: da por sabidas las unidades 1 a 6 (clases, herencia, interfaces y SOLID) y [A1](../a1/index.md) (funciones como valores).

Un **patrón de diseño** es una solución conocida a un problema que se repite al diseñar programas. No es una librería ni código que copiar: es una **forma de organizar clases** que otros programadores ya reconocen. Saber su nombre sirve para algo muy práctico: decir «esto es una fábrica» comunica en tres palabras lo que sin él costaría un párrafo.

Los patrones **de creación** tratan de **cómo se crean los objetos**. Todos los ejemplos de esta unidad usan una cafetería.

## Singleton: una sola instancia

Es una clase de la que **solo puede existir un objeto**, y todos reciben el mismo. Sirve para algo realmente único: la configuración de la aplicación, un registro de mensajes.

En Java el patrón clásico es un **constructor privado** y un método estático que devuelve una instancia guardada en un campo `static final`. La JVM crea esa instancia al cargar la clase y lo hace de forma **segura entre hilos**, así que no hace falta nada más. Con `a == b` se comprueba que son la misma referencia. (Otra forma habitual es un `enum` de un solo valor.)

```java
// Singleton: una clase de la que solo puede existir UNA instancia (aquí, la configuración de la cafetería).
public class Singleton {
    static final class Configuracion {
        // se crea una sola vez, al cargar la clase (y la JVM garantiza que es seguro entre hilos)
        private static final Configuracion UNICA = new Configuracion("Café Central", 10);

        final String local;
        final int iva;

        private Configuracion(String local, int iva) { // constructor privado: nadie de fuera puede crear otra
            this.local = local;
            this.iva = iva;
        }

        static Configuracion obtener() {
            return UNICA;
        }
    }

    public static void main(String[] args) {
        Configuracion a = Configuracion.obtener();
        Configuracion b = Configuracion.obtener();
        System.out.println("misma instancia: " + (a == b ? "sí" : "no"));
        System.out.println("local: " + a.local + ", IVA " + a.iva + "%");
    }
}
```

Salida:

```text
misma instancia: sí
local: Café Central, IVA 10%
```

!!! warning "El patrón más discutido"
    Un singleton es **estado global con buena presentación**: cualquier parte del programa puede cambiarlo, es difícil de sustituir en las pruebas ([A4](../a4/t-aislar.md)) y esconde dependencias. Úsalo solo si de verdad **tiene que** haber uno, y si puedes, **pásalo como parámetro** en lugar de pedírselo a la clase desde cualquier sitio.

## Fábrica: decidir qué clase crear

Una **fábrica** es una función que, a partir de un dato (aquí un nombre), **decide qué clase concreta crear**. Quien la usa solo conoce el tipo común (`Bebida`), no las clases concretas: añadir un tipo nuevo no obliga a cambiar el resto del programa.

El `switch` con flechas devuelve la clase que toca y no «cae» al siguiente caso. Los `record` ahorran el código repetitivo de las clases pequeñas.

```java
import java.util.List;

// Fábrica: un método decide QUÉ clase concreta crear, y quien lo usa solo conoce el tipo común (Bebida).
public class Fabrica {
    interface Bebida {
        String nombre();

        int precio(); // en céntimos
    }

    record Cafe() implements Bebida {
        public String nombre() { return "café"; }
        public int precio() { return 150; }
    }

    record Te() implements Bebida {
        public String nombre() { return "té"; }
        public int precio() { return 120; }
    }

    record Zumo() implements Bebida {
        public String nombre() { return "zumo"; }
        public int precio() { return 200; }
    }

    static Bebida fabricar(String nombre) {
        return switch (nombre) {
            case "café" -> new Cafe();
            case "té" -> new Te();
            case "zumo" -> new Zumo();
            default -> throw new IllegalArgumentException(nombre + ": no está en la carta");
        };
    }

    public static void main(String[] args) {
        for (String nombre : List.of("café", "té", "zumo", "chocolate")) {
            try {
                Bebida bebida = fabricar(nombre);
                System.out.println(bebida.nombre() + ": " + bebida.precio() + " céntimos");
            } catch (IllegalArgumentException e) {
                System.out.println(e.getMessage());
            }
        }
    }
}
```

Salida:

```text
café: 150 céntimos
té: 120 céntimos
zumo: 200 céntimos
chocolate: no está en la carta
```

Es la «D» de SOLID ([unidad 6](../../u06/t-solid-2.md)) en acción: el código depende de una **abstracción** (`Bebida`), no de las clases concretas. Y los datos desconocidos se rechazan **en un solo lugar**, con un mensaje claro.

## Builder: construir paso a paso

Un **builder** construye un objeto con **muchos datos opcionales**, con llamadas encadenadas que dicen qué es cada cosa. Lo que no se indica toma su valor por defecto.

Java no tiene parámetros con nombre ni valores por defecto, por eso el **builder** es tan común: evita constructores con ocho parámetros que nadie sabe en qué orden van. Fíjate en que cada método devuelve **`this`**, lo que permite encadenar las llamadas, y que el constructor de `Pedido` es privado: solo se puede crear a través del builder.

```java
// Builder: construir un objeto con muchos datos opcionales paso a paso, con llamadas encadenadas.
// Es el patrón clásico de Java, que no tiene parámetros con nombre ni valores por defecto.
public class Builder {
    static final class Pedido {
        private final String bebida;
        private final String tamano;
        private final String leche;
        private final boolean azucar;

        private Pedido(PedidoBuilder b) { // solo el builder puede crear un Pedido
            this.bebida = b.bebida;
            this.tamano = b.tamano;
            this.leche = b.leche;
            this.azucar = b.azucar;
        }

        @Override
        public String toString() {
            return bebida + " (" + tamano + "), leche: " + leche + ", " + (azucar ? "con" : "sin") + " azúcar";
        }
    }

    static final class PedidoBuilder {
        private String bebida = "café";
        private String tamano = "pequeño";
        private String leche = "ninguna";
        private boolean azucar = false;

        PedidoBuilder bebida(String b) {
            this.bebida = b;
            return this; // devolver «this» permite encadenar
        }

        PedidoBuilder tamano(String t) {
            this.tamano = t;
            return this;
        }

        PedidoBuilder leche(String l) {
            this.leche = l;
            return this;
        }

        PedidoBuilder conAzucar() {
            this.azucar = true;
            return this;
        }

        Pedido construir() {
            return new Pedido(this);
        }
    }

    public static void main(String[] args) {
        System.out.println(new PedidoBuilder().bebida("capuchino").tamano("grande").leche("avena").construir());
        System.out.println(new PedidoBuilder().conAzucar().construir()); // lo que no se indica toma el valor por defecto
    }
}
```

Salida:

```text
capuchino (grande), leche: avena, sin azúcar
café (pequeño), leche: ninguna, con azúcar
```

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Usar un singleton «porque es cómodo» | Pregúntate si de verdad tiene que haber una sola instancia; si no, pásala como parámetro |
| Una fábrica con un `switch` enorme que crece sin parar | Cuando crezca, usa un diccionario o un registro de clases |
| Un builder para una clase de dos campos | Si bastan parámetros con nombre o un constructor, no lo uses |
| Olvidar validar al final de la construcción | La validación va en `construir()`: un objeto a medias no debería existir |

## Para practicar

Los ejercicios [A5.1, A5.2 y A5.3](ejercicios.md) piden una fábrica de figuras, un registro único y un correo con builder. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
