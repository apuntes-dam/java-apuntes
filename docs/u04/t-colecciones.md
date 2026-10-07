# 4.D Colecciones de objetos y miembros estáticos

## Objetos que contienen objetos

Los objetos se pueden **combinar**: un curso **tiene** alumnos, una biblioteca **tiene** libros, una persona **tiene** cuentas. Es la relación de **composición** («tiene un»), y se resuelve guardando los objetos dentro de otro, normalmente en una **lista**.

```text
Curso ◆────── 0..n ──────▶ Alumno
(guarda una lista)         (nombre, nota)
```

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

class Alumno {
    static int creados = 0;

    final String nombre;
    final int nota;

    Alumno(String nombre, int nota) {
        if (!notaValida(nota)) {
            throw new IllegalArgumentException("nota fuera de rango");
        }
        this.nombre = nombre;
        this.nota = nota;
        creados++;
    }

    static boolean notaValida(int nota) {
        return nota >= 0 && nota <= 10;
    }

    boolean aprobado() {
        return nota >= 5;
    }

    @Override
    public String toString() {
        return nombre + " (" + nota + ")";
    }
}

class Curso {
    private final List<Alumno> alumnos = new ArrayList<>();

    void matricular(Alumno alumno) {
        alumnos.add(alumno);
    }

    int total() {
        return alumnos.size();
    }

    double media() {
        int suma = 0;
        for (Alumno a : alumnos) {
            suma += a.nota;
        }
        return (double) suma / alumnos.size();
    }

    Alumno mejor() {
        Alumno mejor = alumnos.get(0);
        for (Alumno a : alumnos) {
            if (a.nota > mejor.nota) {
                mejor = a;
            }
        }
        return mejor;
    }

    List<Alumno> aprobados() {
        List<Alumno> resultado = new ArrayList<>();
        for (Alumno a : alumnos) {
            if (a.aprobado()) {
                resultado.add(a);
            }
        }
        return resultado;
    }
}

public class Poo4 {
    public static void main(String[] args) {
        Curso curso = new Curso();
        curso.matricular(new Alumno("Ana", 4));
        curso.matricular(new Alumno("Marta", 8));
        curso.matricular(new Alumno("Luis", 6));
        System.out.println("Matriculados: " + curso.total());
        System.out.println("Media del curso: " + curso.media());
        System.out.println("Mejor alumno: " + curso.mejor());
        System.out.println("Aprobados: " + curso.aprobados().stream()
                .map(a -> a.nombre).collect(Collectors.joining(", ")));
        try {
            new Alumno("Error", 11);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
        System.out.println("Alumnos creados: " + Alumno.creados);
    }
}
```

Salida:

```text
Matriculados: 3
Media del curso: 6.0
Mejor alumno: Marta (8)
Aprobados: Marta, Luis
Error: nota fuera de rango
Alumnos creados: 3
```

Qué conviene copiar de este diseño:

* **El curso decide qué se puede hacer con su lista.** Ofrece `matricular`, `media`, `mejor` y `aprobados`, pero **no entrega la lista** para que cualquiera la modifique. `for (Alumno a : alumnos)` o streams. La lista es **`private`**: desde fuera solo se puede matricular o consultar.
* **Cada clase se ocupa de lo suyo.** `Alumno` sabe si está aprobado; `Curso` sabe calcular la media y buscar al mejor. `main` solo **orquesta**.
* **Los recorridos son los de siempre** (acumular una suma, buscar un máximo, filtrar), solo que ahora recorren objetos y consultan sus atributos y propiedades.
* **Las reglas del objeto, en el objeto**: la nota se valida en el constructor de `Alumno`, así un alumno con nota 11 no llega a existir.

## Miembros estáticos

Hasta ahora, cada atributo y cada método pertenecía a **un objeto concreto**. Un miembro **estático** pertenece a la **clase**, y es el mismo para todos los objetos. En el ejemplo hay dos:

* `creados`: un **contador compartido** que cuenta cuántos alumnos se han creado (no tiene sentido guardarlo en cada alumno).
* `notaValida`: una **función de utilidad** que no necesita ningún alumno para funcionar.

En Java se marca con **`static`**: `static int creados = 0;` y `static boolean notaValida(int nota)`. Se usa con el nombre de la clase: `Alumno.creados`, `Alumno.notaValida(5)`. El método `main` es `static` precisamente porque se ejecuta **sin** crear ningún objeto.

!!! warning "No abuses de lo estático"
    Un atributo estático es **estado global**: cualquiera puede cambiarlo y los resultados dependen de lo que haya ocurrido antes en el programa, lo que complica las pruebas. Úsalo para contadores sencillos, constantes y utilidades; no para guardar los datos de la aplicación.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Devolver la lista interna para que otros la modifiquen | Ofrece métodos concretos, o devuelve una copia o una vista de solo lectura |
| Buscar el máximo empezando con un valor «inventado» (como `0`) | Empieza con el primer elemento (y comprueba que la lista no esté vacía) |
| Dividir por el tamaño de una lista vacía | Comprueba antes que hay elementos |
| Poner datos de objetos concretos en un atributo estático | Un atributo estático es para lo que es común a **todos** |

## Para practicar

Las colecciones de objetos y las relaciones entre clases se practican en [U4.3 · POO II](poo-2.md). [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) muestra las mismas ideas en otro lenguaje.
