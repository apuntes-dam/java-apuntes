# 7.D Interfaces gráficas

Hasta ahora los programas **leían** de la consola y **escribían** en ella, en un orden fijo. Una **interfaz gráfica** (GUI) cambia la idea: el programa muestra una **ventana** y **espera** a que el usuario haga algo (escribir, pulsar, elegir). Se dice que está **dirigido por eventos**.

## Ideas comunes a todas las librerías

| Idea | Qué es |
|---|---|
| **Componente** (*widget*) | Cada elemento de la ventana: texto, campo de entrada, botón, lista, imagen |
| **Contenedor y diseño** (*layout*) | Cómo se colocan los componentes: en columna, en fila, en rejilla |
| **Evento** | Algo que ocurre: una pulsación, una tecla, un clic |
| **Controlador de eventos** (*handler* o *callback*) | El código que se ejecuta cuando ocurre el evento |
| **Estado** | Los datos que cambian y que determinan lo que se ve (el texto escrito, el resultado) |
| **Bucle de eventos** | El ciclo interno que espera eventos y los reparte; se pone en marcha al abrir la ventana |

El programa no controla el orden: **reacciona**. Por eso cada botón lleva asociado su controlador.

En Java la librería clásica es **Swing** (`javax.swing`), incluida en el JDK. Se crean **objetos** (`JFrame` para la ventana, `JButton`, `JLabel`, `JTextField`...), se **colocan** dentro de paneles con un *layout* y se les **añade un oyente** (`ActionListener`) que se ejecuta cuando ocurre algo. Es un enfoque **imperativo**: tú creas los componentes y cambias sus propiedades (`resultado.setText(...)`). La otra opción moderna es **JavaFX**, que ya no viene con el JDK.

## Un ejemplo: la propina

La ventana tiene un campo para escribir un importe, un botón «Calcular» y un texto con el resultado (el 10 % del importe). Si lo escrito no es un número entero o es negativo, muestra un mensaje de error.

```java
import java.awt.GridLayout;
import javax.swing.BorderFactory;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTextField;
import javax.swing.SwingUtilities;

public class Gui1 {
    static String calcularPropina(String texto) {
        try {
            int importe = Integer.parseInt(texto.trim());
            if (importe < 0) {
                return "El importe no puede ser negativo";
            }
            return "Propina: " + (importe / 10) + " €";
        } catch (NumberFormatException e) {
            return "Escribe un número entero";
        }
    }

    final JFrame ventana = new JFrame("Propina");
    final JTextField entrada = new JTextField(10);
    final JButton boton = new JButton("Calcular");
    final JLabel resultado = new JLabel(" ");

    Gui1() {
        JPanel panel = new JPanel(new GridLayout(0, 1, 6, 6));
        panel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));
        panel.add(new JLabel("Importe (€):"));
        panel.add(entrada);
        panel.add(boton);
        panel.add(resultado);

        boton.addActionListener(e -> resultado.setText(calcularPropina(entrada.getText())));

        ventana.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        ventana.add(panel);
        ventana.pack();
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new Gui1().ventana.setVisible(true));
    }
}
```

Se ejecuta directamente con `java Gui1.java` (el JDK incluye Swing).

Hay una decisión de diseño importante: **`calcularPropina` es una función aparte**, que recibe un texto y devuelve otro, sin saber nada de ventanas. La pantalla solo se ocupa de **recoger** el texto, **llamar** a la función y **mostrar** el resultado. Esa separación entre **lógica** e **interfaz** hace el código más fácil de probar, de reutilizar (la misma función serviría en consola o en una web) y de cambiar.

## Probar la ventana sin abrirla

Como los componentes son objetos, una prueba puede **escribir en el campo y simular una pulsación** con `doClick()` sin enseñar la ventana.

```java
import javax.swing.SwingUtilities;

/** Prueba de la ventana de Swing: escribe en el campo, pulsa el botón y lee la etiqueta. */
public class Gui1Prueba {
    public static void main(String[] args) throws Exception {
        SwingUtilities.invokeAndWait(() -> {
            Gui1 g = new Gui1();
            for (String texto : new String[] {"50", "abc", "-5"}) {
                g.entrada.setText(texto);
                g.boton.doClick();
                System.out.println(texto + " -> " + g.resultado.getText());
            }
            g.ventana.dispose();
        });
    }
}
```

Los componentes de Swing deben usarse desde el **hilo de eventos** (`EDT`): por eso la prueba lo hace con `SwingUtilities.invokeAndWait`.

Resultado de los tres casos:

```text
50 -> Propina: 5 €
abc -> Escribe un número entero
-5 -> El importe no puede ser negativo
```

He ejecutado esta prueba con Swing en Java 25 y devuelve exactamente esos tres resultados.

## Tres cuidados importantes

* **Nunca bloquees la ventana.** Mientras el controlador de un evento está trabajando, la ventana **no responde**. Las tareas largas (descargas, cálculos pesados, lecturas de archivos grandes) deben hacerse **fuera del hilo de la interfaz**.
* **Valida lo que escribe el usuario.** Todo lo que llega de un campo es **texto**: conviértelo y prevé que falle, como hace `calcularPropina`.
* **Cambia la interfaz desde el sitio correcto.** En Swing, los componentes se crean y se modifican desde el hilo de eventos (`SwingUtilities.invokeLater`).

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| La pantalla no se actualiza al cambiar un dato | Cambiar el estado **del modo que la librería exige** (ver arriba) |
| Escribir la lógica dentro del controlador del botón | Ponerla en una función aparte |
| Congelar la ventana con una tarea larga | Hacerla fuera del hilo de la interfaz |
| Fiarse de que el usuario escribirá un número | Validar y mostrar un mensaje claro |
| Componentes que se salen o se superponen | Usar un contenedor con diseño (columna, rejilla) en vez de posiciones fijas |

## Para practicar

Haz los ejercicios de [U7.4 · Interfaces gráficas](gui.md): una ventana con botón, un contador con estado, un formulario con validación y una lista de tareas. Si quieres profundizar en Compose, tienes la [web de Android y apps móviles](https://apuntes-dam.github.io/android-apuntes/), que trata botones, listas, formularios y navegación. [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) compara el código de cada lenguaje.
