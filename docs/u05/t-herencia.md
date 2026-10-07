# 5.A Herencia

La **herencia** permite crear una clase nueva **a partir de otra ya existente**, aprovechando todo lo que esta tiene y añadiendo o cambiando lo que haga falta. Se usa cuando entre dos clases hay una relación **«es un»**: un perro **es un** animal; un gato **es un** animal.

| Término | Significa | Ejemplo |
|---|---|---|
| **Superclase** (o clase base, o padre) | La clase de la que se hereda | `Animal` |
| **Subclase** (o clase derivada, o hija) | La clase que hereda y la especializa | `Perro`, `Gato` |
| **Sobrescribir** (*override*) | Redefinir un método heredado para que haga otra cosa | `hablar()` |

## Un ejemplo

```java
import java.util.List;

class Animal {
    protected final String nombre;

    Animal(String nombre) {
        this.nombre = nombre;
    }

    String hablar() {
        return "...";
    }

    String presentarse() {
        return nombre + " dice: " + hablar();
    }
}

class Perro extends Animal {
    Perro(String nombre) {
        super(nombre);
    }

    @Override
    String hablar() {
        return "Guau";
    }

    String traerPelota() {
        return nombre + " trae la pelota";
    }
}

class Gato extends Animal {
    Gato(String nombre) {
        super(nombre);
    }

    @Override
    String hablar() {
        return "Miau";
    }

    @Override
    String presentarse() {
        return super.presentarse() + " (y se hace el distraído)";
    }
}

public class Her1 {
    public static void main(String[] args) {
        List<Animal> animales = List.of(new Perro("Rex"), new Gato("Misi"), new Animal("Bicho"));
        for (Animal a : animales) {
            System.out.println(a.presentarse());
        }
        for (Animal a : animales) {
            if (a instanceof Perro p) {
                System.out.println(p.traerPelota());
            }
        }
    }
}
```

Salida:

```text
Rex dice: Guau
Misi dice: Miau (y se hace el distraído)
Bicho dice: ...
Rex trae la pelota
```

Qué ocurre aquí:

* `Perro` y `Gato` **no repiten** el atributo `nombre` ni el método `presentarse`: los reciben de `Animal`.
* Cada subclase **sobrescribe `hablar()`** para dar su propio sonido; `Animal` tiene una versión genérica (`...`), y `Bicho`, que es un `Animal` normal, la usa.
* `Gato` además sobrescribe `presentarse()` y **reutiliza** la versión del padre con `super`, añadiéndole algo.
* `Perro` añade un método que `Animal` no tiene (`traerPelota`). Para llamarlo hay que **comprobar el tipo** antes, porque la lista contiene animales de todo tipo.
* Las tres líneas se imprimen con **la misma llamada** (`a.presentarse()`), pero el resultado depende de **qué objeto concreto** hay detrás. Eso se llama **polimorfismo** y se explica en el [siguiente apartado](t-abstractas.md).

## Cómo se escribe en Java

| Necesito... | En Java |
|---|---|
| Heredar | `class Perro extends Animal { ... }` |
| Constructor del padre | `super(nombre);`, normalmente la primera instrucción. Si no la escribes, Java llama a `super()` sin argumentos. Los constructores **no se heredan** |
| Sobrescribir un método | Repetirlo con la misma firma y `@Override`; el compilador avisa si no coincide |
| Llamar a la versión del padre | `super.presentarse()` |
| Comprobar el tipo | `a instanceof Perro p` (y dentro del `if` ya puedes usar `p`) |
| Impedir que hereden de ti | `final class Perro` |
| ¿Cuántos padres? | **Uno** (herencia simple); se completa con interfaces |
| Visibilidad para las subclases | `protected`: visible en la clase, sus subclases y su paquete |

## ¿Herencia o composición?

La herencia es muy cómoda, pero crea un vínculo fuerte: si la clase base cambia, todas las hijas se ven afectadas. Antes de heredar, pregúntate si la relación es de verdad «es un».

| Relación | Se resuelve con | Ejemplo |
|---|---|---|
| **«es un»** (un perro es un animal) | Herencia | `Perro extends Animal` |
| **«tiene un»** (un coche tiene un motor) | **Composición**: un atributo con otro objeto (ver [4.D](../u04/t-colecciones.md)) | `Coche` con un atributo `Motor` |
| **«sabe hacer»** (un pato sabe volar) | **Interfaz** (ver [5.C](t-interfaces.md)) | `Pato` implementa `Volador` |

!!! warning "No heredes solo para reutilizar código"
    Si `Pila` heredara de `Lista` solo para aprovechar `add`, una pila **no es** una lista: acabaría ofreciendo operaciones que no tienen sentido en ella. En ese caso, la pila **tiene** una lista dentro (composición). Regla práctica: ante la duda, **composición**.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Heredar cuando la relación no es «es un» | Probar la frase «un A es un B» en voz alta |
| Jerarquías muy profundas (A → B → C → D → E) | Mantenerlas cortas; más de tres niveles suele ser mal signo |
| Llamar a un método de la subclase desde una variable del tipo base | Comprobar el tipo antes, o replantear el diseño |
| Olvidar inicializar la parte heredada en el constructor | Llamar siempre al constructor del padre con lo que necesite |

Olvidar `@Override` no impide que compile; pero si te equivocas en el nombre o en los parámetros, **creas un método nuevo** en vez de sobrescribir y nadie te avisa. Escríbelo siempre.

## Para practicar

Los ejercicios de herencia y sobrescritura están en [U5.1](herencia.md) (por ejemplo, los de artículos y vehículos). Compara cómo se escribe en otro lenguaje con [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
