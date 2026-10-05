# U4.2 · POO I (ejercicios 1 al 5)

<div class="ej-gate" data-unit="u04" data-nombre="U4 · Programación orientada a objetos"></div>

Primeras clases: atributos, constructores, validación y métodos.

## Ejercicio 4.1

**Rectángulo.** Crea una clase `Rectangulo` con `base` y `altura`. Debe tener constructor y métodos para calcular el área y el perímetro. Los atributos no se pueden modificar, solo consultar, y deben ser **mayores que 0**. Opcional: `toString`. En el programa principal crea varios rectángulos y muestra sus áreas y perímetros.

<details class="sol" data-key="u345/u4-2/4.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>public class Solucion {
    static class Rectangulo {
        private final double base;
        private final double altura;
        Rectangulo(double base, double altura) {
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 4.2

**Persona e IMC.** Crea la clase `Persona` con nombre, peso (kg), altura (m) e IMC.

* Constructor principal con todos los atributos salvo nombre e IMC. El IMC se **calcula** a partir de peso y altura: se puede consultar pero no modificar.
* Un segundo constructor que también reciba el nombre.
* `toString`.

En `main` crea 3 personas (la primera sin nombre) con ambos constructores y muéstralas. Después:

1. Persona 1: pide un nombre por consola (no vacío), cámbialo y muestra solo nombre, peso y altura.
2. Persona 3: muestra peso, altura e IMC; cambia la altura a 1.80 y vuelve a mostrarlos.
3. Persona 2: dale la misma altura que a la persona 3, muéstralas y **compara** si son iguales (implementa `equals`).

## Ejercicio 4.3

Amplía la clase `Persona` del ejercicio 4.2:

* `saludar()` devuelve un saludo con el nombre.
* `alturaEncimaMedia()` (altura ≥ 1.75) y `pesoEncimaMedia()` (peso ≥ 70).
* `obtenerDescImc()` devuelve el rango: menos de 18.5 → *peso insuficiente*; 18.5 a 24.9 → *peso saludable*; 25.0 a 29.9 → *sobrepeso*; 30.0 o más → *obesidad*. Mejora: usa un enumerado.
* `obtenerDesc()` devuelve algo como: `Julia con una altura de 1.72m (Por debajo de la media) y un peso 64.7kg (Por encima de la media) tiene un IMC de 21,87 (peso saludable)`.

En `main` crea 4 o 5 personas más en una colección y, por cada una, muestra el saludo y la descripción. Por último, pon **privados** los métodos que solo se usen dentro de la clase.

## Ejercicio 4.4

**Coche con validaciones.** Crea la clase `Coche` con color, marca, modelo, caballos, puertas y matrícula.

* Marca y modelo: no vacíos, no nulos, solo se asignan al crear el objeto y se **devuelven con la primera letra en mayúscula**.
* Caballos, puertas y matrícula: no nulos y no modificables. Caballos entre 70 y 700; puertas entre 3 y 5; matrícula de exactamente 7 caracteres.
* Color: se puede modificar, pero no puede ser nulo.
* Constructor y `toString`.

En `main` crea varios coches y comprueba, **capturando las excepciones** que lanza la clase, que no se puede crear un coche con marca/modelo vacíos, caballos o puertas fuera de rango, matrícula de longitud incorrecta ni cambiar el color a nulo.

## Ejercicio 4.5

**Tiempo.** Crea la clase `Tiempo` (de 00:00:00 a 23:59:59) con hora, minuto y segundo. Se puede construir indicando los tres, solo hora y minuto, o solo la hora (lo omitido vale 0). `toString` en formato `XXh XXm XXs`.

* Si segundos o minutos son 60 o más, se reparte hacia la unidad superior (65 s = 1 min 5 s). La hora debe ser menor que 24; si no, lanza una excepción.
* `incrementar(t)`: suma `t`; devuelve `false` (sin cambiar nada) si se pasa de 23:59:59.
* `decrementar(t)`: resta `t`; devuelve `false` (sin cambiar nada) si baja de 00:00:00.
* `comparar(t)`: devuelve -1, 0 o 1.
* `copiar()` devuelve un nuevo `Tiempo` igual; `copiar(t)` copia `t` en el objeto actual.
* `sumar(t)` y `restar(t)`: devuelven un `Tiempo` nuevo, o nulo si el resultado se sale del rango.
* `esMayorQue(t)` y `esMenorQue(t)`.

En `main` pide por teclado hora, minuto y segundo (se pueden omitir los últimos) y prueba todos los métodos.
