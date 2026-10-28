# 2.1 Proceso frente a hilo

Un **proceso** es el contenedor de recursos (memoria, ficheros abiertos, conexiones). Un **hilo** es quien ejecuta las instrucciones. **Todo proceso tiene al menos uno.**

## Un hilo es una línea de ejecución dentro de un proceso

* Un proceso arranca con un **hilo principal**. En Java es el que ejecuta `main`. Desde él se pueden crear más hilos que avancen a la vez.
* Cada hilo puede ejecutarse en un **núcleo distinto**. Con un solo núcleo, se turnan.
* Hay hilos del **sistema operativo** (teclado, red, pantalla…) y hilos de **usuario**, los que creamos nosotros.

Una imagen que ayuda: el **proceso** es la cocina, con su nevera, sus fogones y su despensa. Los **hilos** son varios cocineros en la misma cocina. Cada cocinero lleva su propia libreta de por dónde va: su **pila** y su **contador de programa**.

## Qué comparten los hilos y qué no

| | Dónde vive | ¿Lo ven todos los hilos? |
|---|---|---|
| Los **objetos** (`new …`) | *Heap* | **Sí**, si tienen la referencia |
| Las variables **`static`** | *Heap* / memoria de clase | **Sí** |
| Ficheros abiertos y conexiones de red | El proceso | **Sí** |
| Las variables **locales** | **Pila** de cada hilo | **No**: cada hilo tiene las suyas |
| El contador de programa (por dónde va) | Cada hilo | **No** |

Compartir es **rápido**, pero **peligroso si dos hilos escriben a la vez** (es el tema de la próxima sesión). Este ejemplo enseña la diferencia: un hilo guarda su variable local en el dato compartido, y `main` lo lee; la variable local, en cambio, solo existe en el hilo.

```java
public class Compartido {
    static int[] compartido = new int[1];                 // un objeto: vive en el heap y lo ven todos los hilos

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            int local = 10;                               // variable local: vive en la pila de ESTE hilo
            compartido[0] = local;
            System.out.println("Hilo: mi variable local vale " + local);
        });
        t.start();
        t.join();
        System.out.println("main ve el dato compartido: " + compartido[0]);     // 'local' no existe aquí
    }
}
```

**Salida:**

```text
Hilo: mi variable local vale 10
main ve el dato compartido: 10
```

## Para qué queremos hilos

| Para… | Ejemplo |
|---|---|
| Que el programa **no se congele** | La interfaz sigue respondiendo mientras otro hilo descarga, guarda o calcula |
| **Atender a muchos** a la vez | Un servidor dedica un hilo a cada cliente y no hace esperar a nadie |
| **Usar todos los núcleos** | Un cálculo grande se reparte en trozos, uno por núcleo |

Casi nunca se ven a simple vista: `Future`, las *callbacks*, `async/await` y las corrutinas **funcionan siempre sobre hilos**.

## CPU-bound o IO-bound: qué frena tu programa

| | **CPU-bound** | **IO-bound** |
|---|---|---|
| Pasa el tiempo… | calculando | esperando (red, disco, usuario) |
| Ejemplos | comprimir, cifrar, renderizar | descargar, consultar una base de datos |
| Los hilos ganan… | solo si hay **núcleos libres** | casi siempre: mientras uno espera, otro trabaja |
| ¿Cuántos hilos? | ≈ los **núcleos** disponibles | **muchos más** que núcleos |

Para saber cuántos núcleos ve tu JVM:

```java
public class Nucleos {
    public static void main(String[] args) {
        int nucleos = Runtime.getRuntime().availableProcessors();       // cuántos núcleos ve la JVM
        System.out.println("Esta JVM ve " + nucleos + " núcleos");
    }
}
```

**Salida:**

```text
Esta JVM ve 16 núcleos
```

En tu equipo saldrá el número de tus núcleos. Es el número que sirve de guía para tareas de **cálculo**.

!!! example "Ejercicio de clase"
    El [ejercicio 1](ejercicios.md#ejercicio-1) pide decidir, para cinco situaciones, si usarías un **hilo** o un **proceso nuevo** y si el trabajo es **CPU** o **IO**.
