# U7.4 · Interfaces gráficas

<div class="ej-gate" data-unit="u07" data-nombre="U7 · Entrada/salida y GUI"></div>

De la consola a la ventana: componentes, estado y eventos. Cada lenguaje tiene su propia herramienta; el objetivo es el mismo.

## Ejercicio 7.12

**Hola, ventana.** Crea una ventana con un texto y un botón. Al pulsar el botón, el texto cambia (por ejemplo, de `Hola` a `¡Has pulsado!`).

!!! note "En Java"
    Usa **Swing** (`JFrame`, `JLabel`, `JButton`, `ActionListener`) o **JavaFX**.

## Ejercicio 7.13

**Contador con estado.** Una pantalla con un número, un botón `+` y un botón `-`. El número no puede bajar de 0 y se muestra en rojo si llega a 0. Piensa **dónde guardas el estado** y cómo se actualiza la pantalla.

## Ejercicio 7.14

**Formulario de acceso.** Dos campos (usuario y contraseña) y un botón `Entrar`. Valida que ninguno esté vacío y que la contraseña tenga al menos 6 caracteres; muestra el mensaje de error junto al campo o un `Bienvenido` si todo va bien. (No guardes contraseñas reales.)

## Ejercicio 7.15

**Lista de tareas.** Un campo de texto, un botón `Añadir` y una lista que muestre las tareas añadidas. Opcional: poder marcar una tarea como hecha y borrarla.

!!! note "En Java"
    En Swing: `DefaultListModel` + `JList`.
