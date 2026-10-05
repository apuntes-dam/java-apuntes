# U4.5 · Robots (parte 2 y reto)

<div class="ej-gate" data-unit="u04" data-nombre="U4 · Programación orientada a objetos"></div>

Varios robots con comportamientos distintos.

## Ejercicio 4.13

Crea varios robots en una colección: **R2D2, DAW1A, DAW1B y DAM1**. **Reto:** haz que la clase reciba una *función* que decide cómo cambia la dirección al detenerse (si tu lenguaje no lo facilita, usa otra forma con sentido, por ejemplo una interfaz o una clase por tipo).

* **R2D2**: empieza en (0, 0) mirando a `PositiveY`; al detenerse gira -90°.
* **DAW1A**: x aleatoria entre -5 y 5, y = 0, mirando a `PositiveX`. Al detenerse, si x es positiva gira 180°; si es negativa, 90°.
* **DAW1B**: x = 0, y aleatoria entre -10 y 10, dirección inicial aleatoria. Al detenerse gira -90° si y es positiva y 270° si es negativa.
* **DAM1**: x e y aleatorias entre -5 y 5, dirección inicial aleatoria. Al detenerse toma una dirección aleatoria **distinta** de la actual.

El programa pide el número de movimientos, los ejecuta con todos los robots (enteros entre -20 y 20) y muestra la posición y dirección final de cada uno.
