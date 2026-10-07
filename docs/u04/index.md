# U4 · Programación orientada a objetos

Pasar del código suelto a **clases y objetos**: atributos, métodos, constructores, validación, encapsulamiento y colecciones de objetos. Termina con un proyecto personal o en grupo.

## Teoría

* [4.A Clases y objetos](t-clases.md): atributos, métodos, constructor y referencias.
* [4.B Encapsulamiento](t-encapsulamiento.md): visibilidad, validación, propiedades e igualdad.
* [4.C Constructores y enumerados](t-constructores.md): varias formas de crear un objeto y tipos cerrados.
* [4.D Colecciones y miembros estáticos](t-colecciones.md): objetos que contienen objetos y lo que pertenece a la clase.
* [4.E Del problema al programa](t-modelar.md): cómo decidir las clases y pasar comportamiento como parámetro.

## Antes de empezar: qué debes dominar

* Clase, objeto, atributo y método.
* Constructores (principal y alternativos) y valores por defecto.
* Encapsulamiento: visibilidad y propiedades de solo lectura.
* Enumerados, sobrecarga y `toString`/igualdad.
* Colecciones de objetos y validación con excepciones.

## Ejercicios de la unidad

| Bloque | Ejercicios |
|---|---|
| [U4.1 · Repaso de las unidades 1 a 3](repaso.md) | 1 |
| [U4.2 · POO I (ejercicios 1 al 5)](poo-1.md) | 5 |
| [U4.3 · POO II (ejercicios 6 al 10)](poo-2.md) | 5 |
| [U4.4 · Robots (parte 1)](robots-1.md) | 2 |
| [U4.5 · Robots (parte 2 y reto)](robots-2.md) | 1 |
| [U4.6 · Prueba: Cafetera y Taza](prueba.md) | 2 |
| [U4.7 · Reto personal: gestor de inventario](proyecto.md) | 1 |
| [U4.8 · Cambio de rol: explícamelo tú (grupos)](cambio-de-rol.md) | 2 |

## Equivalencias de POO en Java

| Idea | En Java |
|---|---|
| Constructor principal y sobrecarga | varios constructores con distintos parámetros; `this(...)` encadena |
| Propiedad calculada de solo lectura | método `getImc()` |
| Privado | `private` (y *getters*/*setters* a mano) |
| Validar al crear | `if (...) throw new IllegalArgumentException(...)` en el constructor |
| Herencia | `class B extends A`, con `super(...)` |
| Clase abstracta / interfaz | `abstract class` / `interface` |
| Enumerado | `enum Color { BLANCO, NEGRO }` (admite campos y métodos) |
| Datos con igualdad por valor | `record Persona(String nombre, int edad) {}` |
| Parámetro por defecto | no existe: sobrecarga el método |
| Jerarquía cerrada | `sealed interface ... permits ...` |

## Antes de pasar a los ejercicios

Cuando hayas leído y practicado la teoría, marca la casilla para **desbloquear** los ejercicios de esta unidad:

<div class="ej-check" data-unit="u04"></div>
