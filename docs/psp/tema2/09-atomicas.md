# 2.9 Clases atómicas

Si el problema era que `cuenta++` son tres pasos, la otra solución es una operación que los haga **de una sola vez**, sin que nadie pueda colarse en medio: las **clases atómicas** de `java.util.concurrent.atomic`.

## `AtomicInteger`

```java
import java.util.concurrent.atomic.AtomicInteger;

public class ContadorAtomico {
    static AtomicInteger cuenta = new AtomicInteger(0);

    public static void main(String[] args) throws InterruptedException {
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                for (int j = 0; j < 100_000; j++) cuenta.incrementAndGet();      // en vez de cuenta++
            });
            hilos[i].start();
        }
        for (Thread h : hilos) h.join();
        System.out.println("Esperado 400000, obtenido " + cuenta.get());
    }
}
```

**Salida:**

```text
Esperado 400000, obtenido 400000
```

Una operación **atómica** es indivisible: ningún otro hilo puede verla a medias. `incrementAndGet()` lee, suma y escribe como un único paso, así que el intercalado que perdía incrementos ya no puede ocurrir.

* `AtomicInteger` **no se usa con `++`**, sino con sus métodos: `get()`/`set(v)`, `incrementAndGet()` (suma 1 y devuelve el nuevo valor), `getAndIncrement()` (devuelve el antiguo y luego suma 1), `decrementAndGet()`, `addAndGet(n)`.
* **No usa cerrojos**: el procesador tiene instrucciones que hacen la operación de golpe. Ningún hilo se queda `BLOCKED`.
* Hay también `AtomicLong`, `AtomicBoolean` y `AtomicReference`.

## 8.3 ¿`synchronized` o clase atómica?

| Situación | Usa | Por qué |
|---|---|---|
| Un contador | `AtomicInteger` | Una sola variable y una sola operación: más corto y no bloquea |
| El cajero (comprobar y actuar) | `synchronized` | Comprobar el saldo y restar son dos pasos que deben ir **juntos** |
| Varias variables que cambian a la vez | `synchronized` | P. ej. mover dinero entre dos cuentas: nadie debe ver el dinero restado de una y aún sin sumar en la otra |
| Varios hilos que solo leen | Nada | Leer a la vez es seguro |
| Cada hilo con sus propios datos | Nada | No hay nada compartido (como en el [ejercicio 9](ejercicios.md#ejercicio-9)) |

Las clases atómicas protegen **una operación sobre una variable**. En cuanto la sección crítica tiene varios pasos o varias variables, hace falta un cerrojo.

!!! tip "La mejor sincronización es no necesitarla"
    Si puedes evitar compartir el dato, evítalo: que cada hilo cuente en una variable local y, al final, se sumen los resultados de todos (es lo que hace el [ejercicio 9](ejercicios.md#ejercicio-9)). No hace falta ninguna sincronización mientras trabajan, y suele ser lo más rápido.

!!! example "Ejercicio"
    El [ejercicio 10](ejercicios.md#ejercicio-10) compara, con un mismo contador de visitas, las cuatro formas de la tabla de arriba y sus tiempos.
