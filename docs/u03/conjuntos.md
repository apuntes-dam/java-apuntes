# U3.3 · Conjuntos

<div class="ej-gate" data-unit="u03" data-nombre="U3 · Estructuras de datos"></div>

Colecciones sin elementos repetidos y operaciones de conjuntos (unión, intersección, diferencia).

## Ejercicio 3.3.1

Tienes una lista de compras de un mes, con (cliente, día, monto, domicilio):

```text
("Nuria Costa", 5, 12780.78, "Calle Las Flores 355")
("Jorge Russo", 7, 699, "Mirasol 218")
("Nuria Costa", 7, 532.90, "Calle Las Flores 355")
("Julián Rodriguez", 12, 5715.99, "La Mancha 761")
("Jorge Russo", 15, 958, "Mirasol 218")
```

Escribe una función que reciba la lista y devuelva los domicilios a los que enviar factura, **cada uno una sola vez**.

## Ejercicio 3.3.2

Pide los nombres de los alumnos de primaria (termina con `x`) y después los de secundaria (termina con `x`). Muestra: todos los nombres sin repetir; los que están en ambos niveles; los de primaria que no están en secundaria; y si **todos** los de primaria están también en secundaria.

## Ejercicio 3.3.3

El **conjunto potencia** de S es el conjunto de todos sus subconjuntos. Para `{1, 2, 3}`: `{}, {1}, {2}, {3}, {1,2}, {1,3}, {2,3}, {1,2,3}`. Escribe `conjuntoPotencia(s)` que devuelva la lista de todos los subconjuntos de `s`.

## Ejercicio 3.3.4

Dadas `frutas1 = [manzana, pera, naranja, plátano, uva]` y `frutas2 = [manzana, pera, durazno, sandía, uva]`, crea conjuntos y calcula: las frutas comunes, las que solo están en `frutas1` y las que solo están en `frutas2`.

<details class="sol" data-key="u345/u3-3/3.3.4">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.TreeSet;
public class Solucion {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 3.3.5

Dado el conjunto `{1..10}`, crea el de los pares, el de los múltiplos de 3 y su intersección.

## Ejercicio 3.3.6

Dado el conjunto de vocales, crea el conjunto de consonantes del alfabeto y el de letras comunes entre ambos (debe salir vacío).
