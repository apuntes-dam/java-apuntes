# A3.C Compartir datos con seguridad

El mayor peligro de ejecutar cosas a la vez no es que falle algo de forma evidente, sino que **a veces** salga mal y **a veces** no. Son los fallos más difíciles de encontrar.

## La condición de carrera

En Java varios hilos **comparten** la misma memoria. `contador++` parece una sola operación pero son tres: **leer**, **sumar** y **escribir**. Si dos hilos leen el mismo valor antes de que el otro escriba, una suma **se pierde**: es una **condición de carrera**. Se arregla con un **candado**: `synchronized` (o `ReentrantLock`) deja entrar a **un solo hilo a la vez** en ese bloque. Para un simple contador también vale `AtomicInteger`.

El ejemplo hace que cuatro hilos sumen 1000 veces cada uno, y repite el experimento **tres veces**:

```java
import java.util.ArrayList;
import java.util.List;

// Cuatro hilos suman 1000 veces cada uno a un contador COMPARTIDO.
// Sin protección, «contador++» (leer, sumar, escribir) se mezcla entre hilos y se pierden sumas.
// «synchronized» hace que solo un hilo a la vez entre en ese bloque.
public class Compartir {
    static int contador = 0;
    static final Object CANDADO = new Object();

    static int ejecutar() throws InterruptedException {
        contador = 0;
        List<Thread> hilos = new ArrayList<>();
        for (int i = 0; i < 4; i++) {
            Thread hilo = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    synchronized (CANDADO) {
                        contador++;
                    }
                }
            });
            hilos.add(hilo);
            hilo.start();
        }
        for (Thread hilo : hilos) {
            hilo.join(); // esperar a que termine cada hilo
        }
        return contador;
    }

    public static void main(String[] args) throws InterruptedException {
        List<Integer> resultados = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            resultados.add(ejecutar());
        }
        System.out.println("resultados de 3 ejecuciones: "
                + resultados.stream().map(String::valueOf).reduce((a, b) -> a + " " + b).get());
    }
}
```

Salida:

```text
resultados de 3 ejecuciones: 4000 4000 4000
```

El resultado es **siempre 4000**. Sin protección, ejecutarlo varias veces daría **valores distintos y casi siempre menores**, porque se pierden sumas: lo peligroso es que **a veces coincidiría con 4000 por casualidad** y el fallo pasaría desapercibido.

!!! warning "Probar no demuestra que esté bien"
    Un programa concurrente que funcionó diez veces puede fallar la undécima. Razona sobre **qué datos se comparten** y **quién los modifica**, en vez de fiarte de las pruebas.

## Mejor que compartir: pasarse mensajes

Si dos tareas necesitan intercambiar datos, a menudo es más simple y seguro que **no compartan nada** y se manden mensajes por un canal o cola. Un **productor** genera datos, un **consumidor** los procesa.

Una **`BlockingQueue`** es una cola preparada para hilos: el productor hace `put` y el consumidor `take`, que **espera** si la cola está vacía. Un valor especial (aquí `-1`) avisa de que **ya no hay más**. No hace falta ningún candado: la cola ya se encarga.

```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

// Productor y consumidor: en lugar de compartir una variable, uno mete mensajes en una cola y el otro los saca.
// BlockingQueue ya está preparada para hilos: take() espera si la cola está vacía.
public class Cola {
    static final int FIN = -1; // un valor especial que significa «ya no hay más»

    public static void main(String[] args) throws Exception {
        BlockingQueue<Integer> cola = new LinkedBlockingQueue<>();
        int[] suma = {0};

        Thread consumidor = new Thread(() -> {
            try {
                while (true) {
                    int n = cola.take(); // espera hasta que haya algo
                    if (n == FIN) break;
                    System.out.println("consume " + n);
                    suma[0] += n;
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
        consumidor.start();

        for (int i = 1; i <= 3; i++) {
            Thread.sleep(50); // tarda en tener el dato
            System.out.println("produce " + i);
            cola.put(i);
        }
        cola.put(FIN);
        consumidor.join();
        System.out.println("suma: " + suma[0]);
    }
}
```

Salida:

```text
produce 1
consume 1
produce 2
consume 2
produce 3
consume 3
suma: 6
```

Mira cómo se intercalan `produce` y `consume`: cada dato se consume **en cuanto llega**, sin esperar a que se produzca el siguiente.

## Reglas para dormir tranquilo

| Regla | Por qué |
|---|---|
| **No compartas** si puedes evitarlo: pasa mensajes o devuelve resultados | Sin memoria compartida no hay carreras |
| Si compartes, que sea **inmutable** (unidad [A1.C](../a1/t-composicion.md)) | Lo que no cambia, no se pisa |
| Protege **todo el acceso** al dato compartido, lecturas incluidas | Un solo acceso sin candado reabre el problema |
| Mantén el candado **el menor tiempo posible** | Un candado largo convierte lo concurrente en secuencial |
| Si usas varios candados, **tómalos siempre en el mismo orden** | Si no, dos tareas pueden esperarse la una a la otra para siempre (*interbloqueo* o *deadlock*) |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Proteger la escritura pero no la lectura | Protege ambas con el mismo candado |
| Dar por válido un resultado porque «salió bien al probar» | Razona sobre qué se comparte, no solo pruebes |
| Olvidar avisar al consumidor de que ya no hay más datos | Usa un valor de fin, o cierra el canal |
| Mantener un candado mientras haces una espera larga | Calcula fuera y entra al candado solo para actualizar el dato |

## Para practicar

Los ejercicios [A3.5 y A3.6](ejercicios.md) usan un contador protegido y un productor con consumidor. Para ver la misma idea en los otros lenguajes: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
