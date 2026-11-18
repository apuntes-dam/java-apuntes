# 2.7 Condición de carrera

Todo lo de las páginas anteriores funcionaba porque **cada hilo trabajaba con sus propios datos**. En cuanto dos hilos **escriben** en el mismo dato sin ponerse de acuerdo, el programa falla, y lo hace **sin dar ningún error**.

## `cuenta++` no es una operación, son tres

Lo que en Java parece una sola instrucción, en la máquina son tres pasos: **leer** el valor de `cuenta`, **sumarle** 1 a esa copia y **escribir** el resultado. El planificador puede quitarle la CPU a un hilo **entre cualquiera de los tres pasos**, y otro hilo puede colarse justo ahí:

| t | Hilo A | Hilo B | `cuenta` |
|---|---|---|---|
| 1 | lee 5 | | 5 |
| 2 | | lee 5 | 5 |
| 3 | suma: 6 | | 5 |
| 4 | | suma: 6 | 5 |
| 5 | escribe 6 | | 6 |
| 6 | | escribe 6 | **6, no 7** |

B leyó **antes** de que A escribiera: se han hecho dos incrementos y la variable solo ha subido uno. Pasa pocas veces en cada incremento, pero en 100.000 pasa miles de veces, y es justo lo que viste en la página anterior con `Carrera`.

!!! note "Con un solo núcleo también pasa"
    No hace falta que los hilos se ejecuten a la vez de verdad: basta con que el planificador cambie de hilo entre la lectura y la escritura.

## Las tres palabras del tema

| Concepto | Qué es | En `Carrera` |
|---|---|---|
| **Recurso compartido** | Un dato al que acceden varios hilos: una variable `static`, un objeto, un fichero | `cuenta` |
| **Condición de carrera** | El resultado depende del orden en que se intercalan los hilos | Sale un número distinto cada vez |
| **Sección crítica** | El trozo de código que usa el recurso compartido | `cuenta++` |

!!! tip "Cómo reconocer una condición de carrera"
    Hacen falta **las tres cosas a la vez**: varios hilos, un dato compartido y que **al menos uno escriba**. Si todos solo leen, o cada hilo tiene sus propios datos (como en toda la sesión 1), no hay problema.

## El patrón más peligroso: comprobar y actuar

No hace falta sumar nada para tener una condición de carrera. El patrón **comprobar y actuar** (miro si se cumple algo y, si se cumple, hago algo) es el más peligroso en la vida real: entre mirar y hacer, otro hilo puede cambiar la situación. Un cajero con 100 € de saldo, del que Ana y Luis intentan retirar 80 € cada uno **a la vez**:

```java
public class Cajero {
    static int saldo = 100;

    static void retirar(String quien, int cantidad) {
        if (saldo >= cantidad) {                      // 1. comprobar
            try {
                Thread.sleep(10);                     // el cajero "cuenta los billetes"
            } catch (InterruptedException e) {
                return;
            }
            saldo -= cantidad;                        // 2. actuar
            System.out.println(quien + " retira " + cantidad + ". Saldo: " + saldo);
        } else {
            System.out.println(quien + ": saldo insuficiente");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread ana = new Thread(() -> retirar("Ana", 80));
        Thread luis = new Thread(() -> retirar("Luis", 80));
        ana.start();
        luis.start();
        ana.join();
        luis.join();
        System.out.println("Saldo final: " + saldo);
    }
}
```

**Salida:**

```text
Ana retira 80. Saldo: -60
Luis retira 80. Saldo: -60
Saldo final: -60
```

Los dos entran en el `if` cuando el saldo todavía es 100, así que **los dos pasan la comprobación**, y luego los dos restan. La pausa de 10 ms (el cajero "contando los billetes") hace que el fallo salga casi siempre; sin ella también puede pasar, solo que mucho menos, y por eso este tipo de error se escapa en las pruebas y aparece en producción.

!!! warning "La sección crítica es comprobar **y** actuar"
    Va desde el `if` hasta la resta, ambos incluidos. Proteger solo la resta no arregla nada: los dos hilos ya han comprobado que había saldo cuando llegan ahí.

El mismo patrón aparece al vender la última entrada de un concierto a dos personas o al reservar el mismo asiento de avión dos veces.

## Las colecciones normales tampoco son seguras

`ArrayList` no está pensado para que varios hilos lo modifiquen a la vez: `add()` también son varios pasos por dentro (mirar el hueco, guardar el elemento, aumentar el tamaño).

```java
import java.util.ArrayList;
import java.util.List;

public class ListaInsegura {
    public static void main(String[] args) throws InterruptedException {
        List<Integer> lista = new ArrayList<>();
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                for (int j = 0; j < 10_000; j++) lista.add(j);
            });
            hilos[i].start();
        }
        for (Thread h : hilos) h.join();
        System.out.println("Esperado 40000, obtenido " + lista.size());
    }
}
```

**Salida:**

```text
Esperado 40000, obtenido 22443
```

A veces, en vez de un número incorrecto, salta directamente una excepción como `ArrayIndexOutOfBoundsException`.

!!! example "Ejercicio"
    El [ejercicio 8](ejercicios.md#ejercicio-8) te hace encontrar la condición de carrera (o descartarla) en seis fragmentos distintos. La solución está en [2.8 synchronized y Lock](08-synchronized.md) y en [2.9 Clases atómicas](09-atomicas.md).
