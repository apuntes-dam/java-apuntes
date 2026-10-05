# U6.1 · Jerarquía de clases

<div class="ej-gate" data-unit="u06" data-nombre="U6 · Diseño de programas con POO"></div>

Herencia, clases abstractas, interfaces, modificadores y constructores. La base es una **biblioteca de medios**: `Articulo` (abstracta) con `Libro`, `Revista` y `DVD`, una interfaz `Buscable` y una clase `BibliotecaDigital`.

## Ejercicio 6.1

**Biblioteca de medios.** Crea la jerarquía:

* `Articulo` (abstracta): título, autor y un método abstracto `descripcion()`.
* `Libro` (páginas), `Revista` (número) y `DVD` (duración en minutos), cada una con su `descripcion()`.
* Interfaz `Buscable` con `buscarPorTitulo(titulo)` y `listarPorCategoria(categoria)`.
* `BibliotecaDigital`: guarda una colección de artículos e implementa `Buscable`.

En `main` crea varios artículos, búscalos por título y muestra su descripción.

## Ejercicio 6.2

**Actividad: préstamos.** Amplía la biblioteca del ejercicio 6.1:

1. Añade a `Articulo` una propiedad `disponible` (verdadera por defecto).
2. Implementa `prestarArticulo(titulo)` y `devolverArticulo(titulo)` en `BibliotecaDigital`.
3. Controla los casos erróneos con excepciones propias o mensajes claros: el artículo **no existe**, **ya está prestado** (al prestar) o **ya estaba disponible** (al devolver).
4. En `main` demuestra todos los casos: un préstamo correcto, un préstamo repetido, una devolución correcta y una devolución repetida.

Intenta resolverla antes de mirar la solución.

<details class="sol" data-key="u69/u6-1/6.2">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.List;
public class Solucion {
    static abstract class Articulo {
        final String titulo;
        final String autor;
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 6.3

**Orden de los constructores.** Crea tres clases encadenadas `A` → `B` → `C` (cada una hereda de la anterior). En cada constructor (y en los bloques de inicialización, si tu lenguaje los tiene) imprime un mensaje con el nombre de la clase. **Antes de ejecutar**, escribe en papel qué saldrá al hacer `C()`. Después comprueba y explica el orden. Repite con un constructor que reciba un parámetro y lo pase a la clase padre.

!!! note "En Java"
    Puedes usar también un bloque de inicialización `{ ... }` y un bloque `static { ... }` y observar cuándo se ejecuta cada uno.

## Ejercicio 6.4

**Controlar la herencia.** Crea una clase base con un método que *sí* se pueda sobrescribir y otro que *no*; y una clase que *no* se pueda heredar. Intenta, en cada caso, hacer lo prohibido y copia y explica el error del compilador (o del intérprete). Después añade una clase abstracta y comprueba qué ocurre al intentar instanciarla.

!!! note "En Java"
    Usa `final` (clase o método), `abstract`, `sealed` y `permits`.

## Ejercicio 6.5

**Sobrescritura.** Parte de una clase `Animal` con un método `hacerSonido()`. Crea `Perro`, `Gato` y `Pajaro` que lo sobrescriban. Añade un método `presentarse()` en `Animal` que **reutilice** `hacerSonido()` (el comportamiento cambia según el objeto real). Recorre una lista mixta y explica por qué se ejecuta el método de la subclase.
