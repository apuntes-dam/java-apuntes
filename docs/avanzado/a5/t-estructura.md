# A5.C Estructura y cuándo usar patrones

Los patrones **de estructura** tratan de **cómo se combinan** clases y objetos para formar piezas mayores.

## Decorador: añadir sin modificar

Un **decorador** añade comportamiento a un objeto **envolviéndolo**, sin cambiar su clase ni crear una subclase para cada combinación. Sin él, «café», «café con leche», «café con azúcar» y «café con leche y azúcar» serían **cuatro clases** (y el doble con cada ingrediente nuevo). Con decoradores son **tres**, y las combinaciones salen de apilarlos.

Cada decorador es un **`record` que implementa `Bebida`** y guarda otra `Bebida` (`base`), a la que delega y a la que le añade su parte. Como todos son `Bebida`, se pueden apilar en cualquier número y orden.

```java
// Decorador: un objeto que ES de un tipo y además TIENE otro de ese tipo dentro, al que le añade algo
// sin tocar su clase. Se pueden apilar tantos como se quiera.
public class Decorador {
    interface Bebida {
        String descripcion();

        int precio(); // en céntimos
    }

    record Cafe() implements Bebida {
        public String descripcion() { return "café"; }
        public int precio() { return 150; }
    }

    record ConLeche(Bebida base) implements Bebida {
        public String descripcion() { return base.descripcion() + " con leche"; }
        public int precio() { return base.precio() + 40; }
    }

    record ConAzucar(Bebida base) implements Bebida {
        public String descripcion() { return base.descripcion() + " con azúcar"; }
        public int precio() { return base.precio() + 10; }
    }

    static void mostrar(Bebida bebida) {
        System.out.println(bebida.descripcion() + ": " + bebida.precio() + " céntimos");
    }

    public static void main(String[] args) {
        Bebida bebida = new Cafe();
        mostrar(bebida);
        bebida = new ConLeche(bebida);
        mostrar(bebida);
        bebida = new ConAzucar(bebida);
        mostrar(bebida);
    }
}
```

Salida:

```text
café: 150 céntimos
café con leche: 190 céntimos
café con leche con azúcar: 200 céntimos
```

Cada decorador **añade su parte al resultado del anterior**: el precio sube 40 con la leche y 10 con el azúcar, y la descripción se va completando. Y **el orden importa** cuando la operación no es conmutativa (lo verás en el ejercicio A5.6).

## Otros dos que conviene conocer

| Patrón | Para qué sirve | Ejemplo |
|---|---|---|
| **Adaptador** (*adapter*) | Hacer que una clase **con una interfaz distinta** encaje donde se espera otra, sin modificarla | Una biblioteca antigua devuelve grados Fahrenheit y tu código espera Celsius: el adaptador las conecta |
| **Fachada** (*facade*) | Ofrecer **una interfaz sencilla** delante de un subsistema complicado | Un único `reproducir(archivo)` que por dentro abre, decodifica y envía a los altavoces |

Los dos hacen lo mismo en el fondo: **esconden una complicación** detrás de algo que encaja mejor. La diferencia es qué esconden: el adaptador, una **incompatibilidad**; la fachada, **muchas piezas**.

## ¿Qué patrón uso?

| Tengo este problema | Patrón |
|---|---|
| Debe haber **un único** objeto de algo | Singleton (con mucho cuidado) |
| Decido **qué clase crear** según un dato | Fábrica |
| Construir un objeto con **muchos datos opcionales** | Builder |
| Quiero **cambiar un algoritmo** sin tocar la clase | Estrategia |
| Debo **avisar a quien quiera enterarse** | Observador |
| Quiero **añadir comportamiento** combinable | Decorador |
| Una clase **no encaja** donde la necesito | Adaptador |
| Un subsistema es **complicado de usar** | Fachada |

## Cuándo NO usar un patrón

!!! warning "La «patitis»"
    Los patrones se descubren **después** de ver el problema, no antes. Un programa pequeño con cinco patrones no es más profesional: es **más difícil de leer**. Empieza con el código más simple que funcione, y cuando notes que repites una estructura o que cambiar algo obliga a tocar muchos sitios, **entonces** aplica el patrón que lo arregla.

| Señal | Qué hacer |
|---|---|
| Una clase con un solo método, creada «por si acaso» | Quítala: una función basta |
| Una fábrica con una sola clase | No la necesitas todavía |
| Tres capas de decoradores para algo que cambia una vez | Un `if` es más claro |
| Un patrón que tu lenguaje ya resuelve con una función | Usa la función (estrategia, observador, builder) |

Como has visto en Java, **muchos patrones clásicos se reducen a funciones** cuando el lenguaje las trata como valores ([A1](../a1/index.md)). Los patrones no son un fin: son un **vocabulario común** y unas **soluciones probadas**, y lo importante es el principio de fondo, que ya conoces: **depender de abstracciones y separar lo que cambia de lo que no** ([SOLID](../../u06/t-solid-1.md)).

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Aplicar un patrón porque «queda bien» | Aplícalo cuando resuelva un problema que ya tienes |
| Confundir el nombre con la implementación | El patrón es la **idea**: cada lenguaje la escribe a su manera |
| Apilar decoradores y perder la cuenta de lo que hace cada uno | Pocos decoradores, con nombres claros |
| Copiar la estructura de un libro de otro lenguaje tal cual | Usa lo que tu lenguaje ya ofrece (funciones, `object`, parámetros con nombre) |

## Para practicar

El ejercicio [A5.6](ejercicios.md) pide decoradores de texto y comprobar que el orden importa. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
