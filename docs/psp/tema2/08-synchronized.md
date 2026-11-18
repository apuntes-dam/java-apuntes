# 2.8 `synchronized` y `Lock`

`synchronized` es la herramienta básica de Java para la **exclusión mutua**: que solo **un hilo a la vez** pueda estar dentro de la sección crítica. El hilo que llega y la encuentra ocupada, **espera**.

## Un bloque `synchronized`

```java
public class ContadorSync {
    static int cuenta = 0;
    static final Object cerrojo = new Object();          // uno solo, compartido por todos

    public static void main(String[] args) throws InterruptedException {
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                for (int j = 0; j < 100_000; j++) {
                    synchronized (cerrojo) {
                        cuenta++;                         // sección crítica
                    }
                }
            });
            hilos[i].start();
        }
        for (Thread h : hilos) h.join();
        System.out.println("Esperado 400000, obtenido " + cuenta);
    }
}
```

**Salida:**

```text
Esperado 400000, obtenido 400000
```

Ahora sale **siempre** 400.000.

* Todo objeto de Java lleva un **cerrojo** (también llamado *monitor*) incorporado. `synchronized (cerrojo) { … }` significa: coge el cerrojo de ese objeto, ejecuta el bloque, suéltalo.
* **Al entrar**, el hilo coge el cerrojo; si lo tiene otro, se queda esperando en estado **`BLOCKED`** (el que viste en [2.3](03-ciclo-vida.md)).
* **Al salir** lo suelta, **aunque salga por una excepción**: no hay forma de olvidarse.
* `cerrojo` es `static final`: hay **uno solo**, compartido por todos los hilos. Es la clave de que funcione.

Ahora los hilos hacen cola para incrementar, así que el programa va algo más lento que sin protección. Es normal: **correcto y algo más lento** es mejor que **rápido y mal**.

## Métodos `synchronized`

Poner `synchronized` en la cabecera de un método equivale a envolver todo su cuerpo en un bloque `synchronized`: el objeto **se protege a sí mismo**.

```java
public class ContadorMetodo {
    static class Contador {
        private int valor = 0;

        public synchronized void incrementar() { valor++; }
        public synchronized int getValor() { return valor; }
    }

    public static void main(String[] args) throws InterruptedException {
        Contador c = new Contador();                      // uno, compartido por todos los hilos
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                for (int j = 0; j < 100_000; j++) c.incrementar();
            });
            hilos[i].start();
        }
        for (Thread h : hilos) h.join();
        System.out.println("Esperado 400000, obtenido " + c.getValor());
    }
}
```

**Salida:**

```text
Esperado 400000, obtenido 400000
```

| Escribes | Cerrojo que usa |
|---|---|
| `synchronized void m()` | `this`: el objeto sobre el que se llama |
| `static synchronized void m()` | La clase (`Contador.class`) |
| `synchronized (obj) { … }` | `obj` |

Todos los hilos deben llamar a los métodos **del mismo objeto** `Contador`: si cada hilo creara el suyo, cada uno tendría su propio cerrojo y no se protegerían entre sí. `getValor()` también va `synchronized`: leer mientras otro hilo escribe tampoco es seguro.

## Arreglar el cajero

En el cajero de la página anterior, `saldo` y `retirar` son `static`, así que el cerrojo natural es la clase:

```java
public class CajeroSync {
    static int saldo = 100;

    static synchronized void retirar(String quien, int cantidad) {
        if (saldo >= cantidad) {
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                return;
            }
            saldo -= cantidad;
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
Ana retira 80. Saldo: 20
Luis: saldo insuficiente
Saldo final: 20
```

Ahora, mientras Ana está dentro del método, Luis espera en `BLOCKED`. Cuando entra, el saldo ya ha bajado y el `if` falla: puede retirar primero Luis en vez de Ana, pero **nunca retiran los dos**.

## El error más común: un cerrojo por hilo

```java
public class CerrojoMalo {
    static int cuenta = 0;

    public static void main(String[] args) throws InterruptedException {
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                Object cerrojo = new Object();            // ¡cada hilo crea el suyo!
                for (int j = 0; j < 100_000; j++) {
                    synchronized (cerrojo) {
                        cuenta++;
                    }
                }
            });
            hilos[i].start();
        }
        for (Thread h : hilos) h.join();
        System.out.println("Esperado 400000, obtenido " + cuenta);
    }
}
```

**Salida:**

```text
Esperado 400000, obtenido 328673
```

Compila, parece protegido… y falla igual que sin protección. Cada hilo coge **su propio** cerrojo, así que nunca tiene que esperar a nadie: es como si no hubiera cerrojo.

!!! tip "Dos reglas"
    1. **Un único cerrojo**, compartido por todos los hilos que tocan el dato.
    2. **Sección crítica pequeña**: mientras un hilo tiene el cerrojo, los demás están parados. Si dentro metes algo que no necesitaba protección (un `sleep`, una descarga), todos esperan por nada.

## `Lock`: el mismo cerrojo, pero como un objeto

`synchronized` es una palabra clave: el cerrojo se coge y se suelta solo, sin que puedas elegir cuándo. `java.util.concurrent.locks.Lock` (normalmente con la implementación `ReentrantLock`) hace **lo mismo**, exclusión mutua, pero como un **objeto** que tú pides y sueltas explícitamente, con más control:

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ContadorLock {
    static int cuenta = 0;
    static final Lock cerrojo = new ReentrantLock();

    public static void main(String[] args) throws InterruptedException {
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                for (int j = 0; j < 100_000; j++) {
                    cerrojo.lock();
                    try {
                        cuenta++;
                    } finally {
                        cerrojo.unlock();
                    }
                }
            });
            hilos[i].start();
        }
        for (Thread h : hilos) h.join();
        System.out.println("Esperado 400000, obtenido " + cuenta);
    }
}
```

**Salida:**

```text
Esperado 400000, obtenido 400000
```

Igual que con `synchronized`, sale **siempre** 400.000. La diferencia está en cómo se escribe:

* `cerrojo.lock()` **pide** el cerrojo (si está ocupado, espera); `cerrojo.unlock()` lo **suelta**.
* A diferencia de `synchronized`, `Lock` **no suelta solo** si salta una excepción: hay que soltarlo a mano en un bloque `finally`. **Olvidarte del `finally` dejaría el cerrojo cogido para siempre** y los demás hilos esperando sin fin: es el precio de tener más control.
* A cambio, `Lock` ofrece cosas que `synchronized` no tiene, como `tryLock()`.

## `tryLock()`: pedir el cerrojo sin esperar

`lock()` espera indefinidamente si el cerrojo está ocupado. `tryLock()` lo **pide y vuelve al instante**, diga que sí o que no, sin quedarse `BLOCKED`:

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class PruebaTryLock {
    static final Lock cerrojo = new ReentrantLock();

    public static void main(String[] args) throws InterruptedException {
        Thread ocupante = new Thread(() -> {
            cerrojo.lock();
            try {
                System.out.println("Ocupante: tengo el cerrojo, trabajo 300 ms");
                Thread.sleep(300);
            } catch (InterruptedException e) {
            } finally {
                cerrojo.unlock();
                System.out.println("Ocupante: he soltado el cerrojo");
            }
        });
        ocupante.start();
        Thread.sleep(50);                              // deja tiempo a que el ocupante coja el cerrojo primero

        Thread impaciente = new Thread(() -> {
            boolean conseguido = cerrojo.tryLock();
            System.out.println("Impaciente: ¿conseguí el cerrojo al primer intento? " + conseguido);
            if (conseguido) cerrojo.unlock();
        });
        impaciente.start();
        impaciente.join();
        ocupante.join();

        boolean ahora = cerrojo.tryLock();
        System.out.println("Después de que el ocupante termine, ¿se consigue? " + ahora);
        if (ahora) cerrojo.unlock();
    }
}
```

**Salida:**

```text
Ocupante: tengo el cerrojo, trabajo 300 ms
Impaciente: ¿conseguí el cerrojo al primer intento? false
Ocupante: he soltado el cerrojo
Después de que el ocupante termine, ¿se consigue? true
```

* `tryLock()` devuelve `true` si **consiguió** el cerrojo (y entonces hay que soltarlo luego, como siempre) o `false` si estaba ocupado — **no espera a que se libere**.
* Es útil cuando un hilo tiene algo mejor que hacer que esperar: puede intentarlo, y si no lo consigue, seguir con otra tarea y probar más tarde.
* También existe `tryLock(tiempo, unidad)`: espera como máximo ese tiempo antes de rendirse, a medio camino entre `lock()` (espera siempre) y `tryLock()` (no espera nada).

!!! example "Ejercicio"
    El [ejercicio 9](ejercicios.md#ejercicio-9) suma un array de cien millones de enteros repartido entre varios hilos, sin que haga falta ningún `synchronized`. ¿Por qué no hace falta, si todos escriben en el mismo array? La clave está en [2.9 Clases atómicas](09-atomicas.md#83-synchronized-o-clase-atomica). El [ejercicio 13](ejercicios.md#ejercicio-13) compara las tres formas de proteger un contador: `synchronized`, `Lock` y `tryLock`.
