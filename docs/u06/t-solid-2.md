# 6.C SOLID: Liskov, interfaces pequeñas e inversión de dependencias

Los tres principios restantes de [SOLID](t-solid-1.md). Cada ejemplo vuelve a mostrar la versión «antes» y la «después», con el mismo resultado visible.

## L · Sustitución de Liskov

Donde se pida una clase base, **tiene que poder usarse cualquiera de sus hijas** sin que el programa falle ni se comporte de forma rara. Si una hija **rompe** lo que la base prometía, la herencia está mal planteada, aunque la frase «es un» suene bien.

```java
import java.util.List;

// ANTES: DocumentoSoloLectura "es un" Documento, pero NO se puede usar en su lugar
class Documento {
    final String nombre;
    String contenido;

    Documento(String nombre, String contenido) {
        this.nombre = nombre;
        this.contenido = contenido;
    }

    void guardar(String texto) {
        contenido = texto;
    }
}

class DocumentoSoloLectura extends Documento {
    DocumentoSoloLectura(String nombre, String contenido) {
        super(nombre, contenido);
    }

    @Override
    void guardar(String texto) {
        throw new UnsupportedOperationException("es de solo lectura");
    }
}

// DESPUÉS: la jerarquía refleja lo que cada clase SABE hacer
abstract class Lectura {
    final String nombre;
    protected String contenido;

    Lectura(String nombre, String contenido) {
        this.nombre = nombre;
        this.contenido = contenido;
    }

    String leer() {
        return contenido;
    }
}

class Editable extends Lectura {
    Editable(String nombre, String contenido) {
        super(nombre, contenido);
    }

    void guardar(String texto) {
        contenido = texto;
    }
}

class SoloLectura extends Lectura {
    SoloLectura(String nombre, String contenido) {
        super(nombre, contenido);
    }
}

public class Soll {
    public static void main(String[] args) {
        List<Documento> antes = List.of(new Documento("ficha", "v1"), new DocumentoSoloLectura("contrato", "texto original"));
        for (Documento d : antes) {
            try {
                d.guardar("v2");
                System.out.println("[antes] " + d.nombre + ": guardado");
            } catch (UnsupportedOperationException e) {
                System.out.println("[antes] " + d.nombre + ": Error: " + e.getMessage());
            }
        }

        // solo se pueden guardar documentos Editable: con SoloLectura ni siquiera compilaría
        List<Editable> editables = List.of(new Editable("ficha", "v1"));
        for (Editable d : editables) {
            d.guardar("v2");
            System.out.println("[después] " + d.nombre + ": guardado");
        }
        SoloLectura contrato = new SoloLectura("contrato", "texto original");
        System.out.println("[después] " + contrato.nombre + " se puede leer: " + contrato.leer());
    }
}
```

Salida:

```text
[antes] ficha: guardado
[antes] contrato: Error: es de solo lectura
[después] ficha: guardado
[después] contrato se puede leer: texto original
```

* **Antes:** `DocumentoSoloLectura` «es un» `Documento`, pero cuando se le pide guardar **falla**. Un bucle que guarda todos los documentos funciona con unos y se rompe con otros: la hija **no puede sustituir** a la base.
* **Después:** la jerarquía refleja lo que cada clase **sabe hacer**. Todos se pueden **leer** (`Lectura`); solo los `Editable` se pueden **guardar**. Ya no hay forma de pedir guardar a un documento de solo lectura.

**Señales de que se viola:** una hija que lanza «no soportado», que deja un método vacío, o código que necesita comprobar el tipo concreto antes de usar la clase base.

## I · Segregación de interfaces

Es mejor tener **varias interfaces pequeñas** y específicas que una grande. Ninguna clase debería verse obligada a implementar métodos que no usa.

```java
// ANTES: una interfaz «gorda» obliga a implementar lo que no se usa
interface Maquina {
    String imprimir(String texto);

    String escanear(String texto);
}

class ImpresoraSencillaMala implements Maquina {
    public String imprimir(String texto) {
        return "impreso: " + texto;
    }

    public String escanear(String texto) {
        throw new UnsupportedOperationException("no soportado");
    }
}

// DESPUÉS: contratos pequeños; cada clase cumple solo los suyos
interface Impresora {
    String imprimir(String texto);
}

interface Escaner {
    String escanear(String texto);
}

class ImpresoraSencilla implements Impresora {
    public String imprimir(String texto) {
        return "impreso: " + texto;
    }
}

class Multifuncion implements Impresora, Escaner {
    public String imprimir(String texto) {
        return "impreso: " + texto;
    }

    public String escanear(String texto) {
        return "escaneado: " + texto;
    }
}

public class Soli {
    public static void main(String[] args) {
        Maquina vieja = new ImpresoraSencillaMala();
        System.out.println("[antes] sencilla imprime: " + vieja.imprimir("informe"));
        try {
            vieja.escanear("foto");
        } catch (UnsupportedOperationException e) {
            System.out.println("[antes] sencilla escanea: Error: " + e.getMessage());
        }

        ImpresoraSencilla sencilla = new ImpresoraSencilla();
        Multifuncion multi = new Multifuncion();
        System.out.println("[después] sencilla imprime: " + sencilla.imprimir("informe"));
        // sencilla.escanear("foto");  // ya no compila: la clase no promete escanear
        System.out.println("[después] multifunción escanea: " + multi.escanear("foto"));
    }
}
```

Salida:

```text
[antes] sencilla imprime: impreso: informe
[antes] sencilla escanea: Error: no soportado
[después] sencilla imprime: impreso: informe
[después] multifunción escanea: escaneado: foto
```

* **Antes:** la interfaz `Maquina` obliga a toda impresora a «escanear», aunque no pueda: la implementación acaba lanzando un error.
* **Después:** `Impresora` y `Escaner` son contratos separados. La impresora sencilla cumple uno; la multifunción, los dos. Pedir escanear a una impresora sencilla **ni siquiera se puede escribir**.

**Señales de que se viola:** métodos que lanzan «no soportado» o que están vacíos solo para cumplir la interfaz.

## D · Inversión de dependencias

Las clases importantes no deberían depender de **detalles concretos** (el reloj del sistema, una base de datos, un servicio de correo), sino de **abstracciones** (un contrato) que se les entregan **desde fuera**. Así se puede cambiar el detalle o, muy importante, **sustituirlo por uno falso en las pruebas**.

```java
import java.time.LocalTime;

// ANTES: el Saludador crea su propio reloj: no se puede probar con una hora concreta
class SaludadorMalo {
    String saludo() {
        int hora = LocalTime.now().getHour();
        if (hora < 12) return "Buenos días";
        if (hora < 20) return "Buenas tardes";
        return "Buenas noches";
    }
}

// DESPUÉS: depende de una abstracción que le dan desde fuera
interface Reloj {
    int hora();
}

class RelojReal implements Reloj {
    public int hora() {
        return LocalTime.now().getHour();
    }
}

class RelojFijo implements Reloj {
    private final int hora;

    RelojFijo(int hora) {
        this.hora = hora;
    }

    public int hora() {
        return hora;
    }
}

class Saludador {
    private final Reloj reloj;

    Saludador(Reloj reloj) {
        this.reloj = reloj;
    }

    String saludo() {
        int hora = reloj.hora();
        if (hora < 12) return "Buenos días";
        if (hora < 20) return "Buenas tardes";
        return "Buenas noches";
    }
}

public class Sold {
    public static void main(String[] args) {
        new SaludadorMalo().saludo();
        System.out.println("[antes] el resultado depende de la hora del equipo");
        for (int hora : new int[] {9, 15, 22}) {
            System.out.println("[después] " + hora + " h -> " + new Saludador(new RelojFijo(hora)).saludo());
        }
        String real = new Saludador(new RelojReal()).saludo();
        System.out.println("con el reloj real: " + (!real.isEmpty() ? "funciona" : "falla"));
    }
}
```

Salida:

```text
[antes] el resultado depende de la hora del equipo
[después] 9 h -> Buenos días
[después] 15 h -> Buenas tardes
[después] 22 h -> Buenas noches
con el reloj real: funciona
```

* **Antes:** `SaludadorMalo` pregunta la hora al sistema. Para comprobar que a las 22 h dice «Buenas noches» habría que esperar a las 22 h: no se puede probar.
* **Después:** `Saludador` recibe un `Reloj` (un contrato). En el programa real se le da el `RelojReal`; en las pruebas, un `RelojFijo` con la hora que se quiera. El `Saludador` **no cambia**.

A esta técnica de entregar las dependencias desde fuera, normalmente por el **constructor**, se le llama **inyección de dependencias**. Fíjate en que `Saludador` no escribe `new RelojReal()` por dentro: *recibe* un `Reloj`.

!!! tip "Un objeto falso se llama «doble de prueba»"
    `RelojFijo` es un **doble de prueba** (*fake*): hace de reloj pero controlas lo que devuelve. Es la base de las pruebas automáticas serias, y solo es posible si se diseña con este principio.

## Resumen de los cinco principios

| Principio | Señal de que falla | Remedio habitual |
|---|---|---|
| **S** | Una clase hace varias cosas («y») | Dividirla; una clase coordina |
| **O** | Cadena de `if` según un tipo que crece | Interfaz + una clase por variante |
| **L** | Una hija lanza «no soportado» o necesita comprobaciones de tipo | Rehacer la jerarquía según lo que **sabe hacer** cada clase |
| **I** | Métodos vacíos o que fallan solo para cumplir la interfaz | Dividir la interfaz |
| **D** | La clase crea por dentro sus dependencias y no se puede probar | Recibir una abstracción por el constructor |

## Para practicar

Haz los ejercicios L, I y D de [U6.2 · SOLID](solid.md): en todos hay que partir de un código con el problema y refactorizarlo, como en estos ejemplos. [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) compara cómo se escriben interfaces y funciones en cada lenguaje.
