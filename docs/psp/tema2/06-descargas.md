# 2.6 Práctica guiada: gestor de descargas

Cinco descargas, **cuatro versiones**. Cada fichero tarda unos segundos (se simula con `sleep`):

| Fichero | `foto.jpg` | `video.mp4` | `musica.mp3` | `juego.zip` | `apuntes.pdf` |
|---|---|---|---|---|---|
| Segundos | 1 | 4 | 2 | 2 | 1 |

El `main` mide el tiempo total y llama a **una sola** función, que cambiamos en cada paso. Todo lo que cambia entre versiones es esa función.

## Paso 1 · Descargar una detrás de otra

La versión secuencial: todo en el hilo `main`. El total es la **suma**: 1 + 4 + 2 + 2 + 1 = **10 s**. Fíjate en `descargar`: dice qué hilo la hace y tiene un `catch` que usaremos en el paso 4.

```java
import java.util.ArrayList;
import java.util.List;

public class Descargas {
    static final String[] FICHEROS = {"foto.jpg", "video.mp4", "musica.mp3", "juego.zip", "apuntes.pdf"};
    static final int[] SEGUNDOS = {1, 4, 2, 2, 1};

    // Simula una descarga: tarda 'seg' segundos y dice qué hilo la hace
    static void descargar(String nombre, int seg) {
        String hilo = Thread.currentThread().getName();
        System.out.printf("[%s] empieza %s%n", hilo, nombre);
        try {
            Thread.sleep(seg * 1000L);
            System.out.printf("[%s] terminado %s (%d s)%n", hilo, nombre, seg);
        } catch (InterruptedException e) {
            System.out.printf("[%s] CANCELADO %s%n", hilo, nombre);          // lo usaremos en el paso 4
        }
    }

    static void secuencial() {
        for (int i = 0; i < FICHEROS.length; i++) descargar(FICHEROS[i], SEGUNDOS[i]);
    }

    public static void main(String[] args) throws InterruptedException {
        long ini = System.currentTimeMillis();
        secuencial();                                          // en cada paso cambiamos esta llamada
        long ms = System.currentTimeMillis() - ini;
        System.out.printf("Total: %.1f s%n", ms / 1000.0);
    }
}
```

**Salida:**

```text
[main] empieza foto.jpg
[main] terminado foto.jpg (1 s)
[main] empieza video.mp4
[main] terminado video.mp4 (4 s)
[main] empieza musica.mp3
[main] terminado musica.mp3 (2 s)
[main] empieza juego.zip
[main] terminado juego.zip (2 s)
[main] empieza apuntes.pdf
[main] terminado apuntes.pdf (1 s)
Total: 10,1 s
```

## Paso 2 · Un hilo por descarga

Se crea un hilo por fichero, se guardan en una lista y se espera a todos **después**. El orden de las líneas cambia, pero ahora el total es lo que tarda **la más lenta**, no la suma.

```java
    static void conHilos() throws InterruptedException {
        List<Thread> hilos = new ArrayList<>();
        for (int i = 0; i < FICHEROS.length; i++) {
            int n = i;
            Thread t = new Thread(() -> descargar(FICHEROS[n], SEGUNDOS[n]), "D-" + n);
            hilos.add(t);
            t.start();
        }
        for (Thread t : hilos) t.join();
    }
```

**Salida:**

```text
[D-0] empieza foto.jpg
[D-3] empieza juego.zip
[D-2] empieza musica.mp3
[D-1] empieza video.mp4
[D-4] empieza apuntes.pdf
[D-0] terminado foto.jpg (1 s)
[D-4] terminado apuntes.pdf (1 s)
[D-3] terminado juego.zip (2 s)
[D-2] terminado musica.mp3 (2 s)
[D-1] terminado video.mp4 (4 s)
Total: 4,0 s
```

## Paso 3 · Cambiar a hilos virtuales

**Mismo tiempo, mismo `join`.** Solo cambia cómo se crea el hilo. Con cinco descargas no se nota; con 100.000, solo la versión virtual aguanta.

```java
    static void conVirtuales() throws InterruptedException {
        List<Thread> hilos = new ArrayList<>();
        for (int i = 0; i < FICHEROS.length; i++) {
            int n = i;
            Thread t = Thread.ofVirtual().name("V-" + n)                 // lo único que cambia
                    .start(() -> descargar(FICHEROS[n], SEGUNDOS[n]));
            hilos.add(t);
        }
        for (Thread t : hilos) t.join();
    }
```

**Salida:**

```text
[V-0] empieza foto.jpg
[V-1] empieza video.mp4
[V-3] empieza juego.zip
[V-2] empieza musica.mp3
[V-4] empieza apuntes.pdf
[V-4] terminado apuntes.pdf (1 s)
[V-0] terminado foto.jpg (1 s)
[V-3] terminado juego.zip (2 s)
[V-2] terminado musica.mp3 (2 s)
[V-1] terminado video.mp4 (4 s)
Total: 4,0 s
```

## Paso 4 · Cancelar lo que pase de 3 segundos

Un **solo límite para todas**: cada `join` espera solo lo que queda hasta el límite. Lo que sigue vivo se **cancela con `interrupt()`**, y el `catch` de `descargar` lo cuenta. Ojo: `join(0)` espera **para siempre**, por eso el `if (queda > 0)`.

```java
    static void conLimite() throws InterruptedException {
        List<Thread> hilos = new ArrayList<>();
        for (int i = 0; i < FICHEROS.length; i++) {
            int n = i;
            hilos.add(Thread.ofVirtual().name("V-" + n)
                    .start(() -> descargar(FICHEROS[n], SEGUNDOS[n])));
        }
        long limite = System.currentTimeMillis() + 3000;
        for (Thread t : hilos) {
            long queda = limite - System.currentTimeMillis();
            if (queda > 0) t.join(queda);                     // espera como mucho lo que queda
            if (t.isAlive()) t.interrupt();                   // se pasó: cancelar
        }
        for (Thread t : hilos) t.join();                      // que los cancelados acaben
    }
```

**Salida:**

```text
[V-1] empieza video.mp4
[V-0] empieza foto.jpg
[V-2] empieza musica.mp3
[V-3] empieza juego.zip
[V-4] empieza apuntes.pdf
[V-4] terminado apuntes.pdf (1 s)
[V-0] terminado foto.jpg (1 s)
[V-2] terminado musica.mp3 (2 s)
[V-3] terminado juego.zip (2 s)
[V-1] CANCELADO video.mp4
Total: 3,0 s
```

## Balance

| Paso | Versión | Lo que usamos | Total |
|---|---|---|---|
| 1 | Secuencial | solo el hilo `main` | ≈ 10 s |
| 2 | Un hilo por descarga | `new Thread`, `start`, `join` | ≈ 4 s |
| 3 | Hilos virtuales | `Thread.ofVirtual()` | ≈ 4 s |
| 4 | Con límite de 3 s | `join(ms)`, `isAlive`, `interrupt` | ≈ 3 s |

Las tareas que **esperan** son las que más ganan con hilos. Y **ninguna de estas compartía datos** con las demás.

## Adelanto de la próxima sesión: cuatro hilos suman 1 a la misma variable

Aquí sí se comparte un dato, y sale mal. Cuatro hilos suman 1, cien mil veces cada uno, a la **misma variable**. Debería salir 400.000:

```java
public class Carrera {
    static int cuenta = 0;                                // compartida: está en el heap

    public static void main(String[] args) throws InterruptedException {
        Thread[] hilos = new Thread[4];
        for (int i = 0; i < 4; i++) {
            hilos[i] = new Thread(() -> {
                for (int j = 0; j < 100_000; j++) cuenta++;
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
Esperado 400000, obtenido 127833
```

Y **cada vez sale un número distinto**. ¿Dónde se pierden las sumas que faltan? Es la **condición de carrera**, la próxima sesión. Mientras tanto, en el modo Avanzado tienes [A3.C Compartir datos con seguridad](../../avanzado/a3/t-compartir.md).
