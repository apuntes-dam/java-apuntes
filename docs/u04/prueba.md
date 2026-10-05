# U4.6 · Prueba: Cafetera y Taza

<div class="ej-gate" data-unit="u04" data-nombre="U4 · Programación orientada a objetos"></div>

Práctica de clase de POO: constructores, sobrecarga, enumerados y colecciones.

## Ejercicio 4.14

**Clase `Cafetera`** con `ubicacion`, `capacidad` (máx. 1000 c.c. por defecto) y `cantidad` (0 al empezar).

* Constructor principal con la ubicación.
* Constructor con ubicación y capacidad: la cafetera empieza llena.
* Constructor con ubicación, capacidad y cantidad: si la cantidad supera la capacidad, se ajusta al máximo.
* `llenar()`, `vaciar()`.
* `servirTaza(taza)`: llena la taza con su capacidad y resta de la cafetera; si no alcanza, sirve lo que quede.
* `agregarCafe(cantidad = 200)`: nunca supera la capacidad.
* `toString`: `Cafetera(ubicación = Salón, capacidad = 1000 c.c., cantidad = 0 c.c.)`.

**Clase `Taza`** con `color` (blanco por defecto), `capacidad` (50 c.c. por defecto) y `cantidad` (0). Si se asigna una cantidad mayor que la capacidad, se queda en la capacidad. `llenar()` y `llenar(cantidad)` (sobrecarga) y `toString`: `Taza(color = BLANCO, capacidad = 50 c.c., cantidad = 30 c.c.)`.

**Enumerado `Color`**: blanco, negro, gris, azul y verde.

## Ejercicio 4.15

**Programa principal.**

1. Crea 3 cafeteras (Sala, Cocina y Oficina) de capacidad 1000, 750 y 500 c.c. con 0, 750 y 200 c.c. de cantidad, cada una con un constructor distinto.
2. Crea una lista de 20 tazas con capacidad aleatoria de 50, 75 o 100.
3. Muestra las cafeteras y las tazas.
4. Llena la cafetera 1, vacía la 2, añade a la 2 la mitad de su capacidad y añade 400 c.c. a la 3. Muestra las cafeteras.
5. Sirve café en las tazas, en orden cafetera 1, 2 y 3, mientras quede café. Muestra el resultado final.
