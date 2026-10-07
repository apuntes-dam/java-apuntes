# 6.B SOLID: responsabilidad única y abierto/cerrado

**SOLID** es el nombre de **cinco principios de diseño** con objetos. No son leyes ni se aplican al pie de la letra, pero resumen lo que se ha comprobado que hace el código más fácil de **entender, cambiar y probar**. Cada letra es un principio:

| Letra | Principio | En una frase |
|---|---|---|
| **S** | Responsabilidad única | Una clase, **una razón para cambiar** |
| **O** | Abierto/cerrado | Ampliar **sin modificar** lo que ya funciona |
| **L** | Sustitución de Liskov | Una hija debe poder usarse **donde se pida su base** sin sorpresas |
| **I** | Segregación de interfaces | **Contratos pequeños**, no obligar a implementar lo que no se usa |
| **D** | Inversión de dependencias | Depender de **abstracciones**, no de clases concretas |

En esta página, las dos primeras; en la [siguiente](t-solid-2.md), las otras tres. En cada ejemplo hay primero una versión **«antes»** con el problema y después una versión **«después»** ya corregida.

!!! info "Cómo leer los ejemplos"
    Los dos programas hacen **lo mismo** y producen el mismo resultado. Lo que cambia es **cómo está organizado el código**, y eso se nota cuando hay que modificarlo.

## S · Responsabilidad única

Una clase debería tener **una sola razón para cambiar**. Si una clase hace varias cosas (validar, guardar, avisar), cualquier cambio en una de ellas obliga a tocar la clase y puede romper las demás.

```java
import java.util.ArrayList;
import java.util.List;

// ANTES: una sola clase valida, guarda y avisa (tres responsabilidades)
class RegistroMalo {
    final List<String> usuarios = new ArrayList<>();
    final List<String> enviados = new ArrayList<>();

    String registrar(String correo) {
        if (!correo.contains("@")) {
            return "correo no válido"; // validar
        }
        usuarios.add(correo); // guardar
        enviados.add("Bienvenido, " + correo); // avisar
        return "registrado";
    }
}

// DESPUÉS: cada clase tiene una sola razón para cambiar
class ValidadorCorreo {
    boolean esValido(String correo) {
        return correo.contains("@");
    }
}

class RepositorioUsuarios {
    private final List<String> correos = new ArrayList<>();

    void guardar(String correo) {
        correos.add(correo);
    }

    int total() {
        return correos.size();
    }
}

class Avisador {
    final List<String> enviados = new ArrayList<>();

    void bienvenida(String correo) {
        enviados.add("Bienvenido, " + correo);
    }
}

class ServicioRegistro {
    private final ValidadorCorreo validador;
    private final RepositorioUsuarios repositorio;
    private final Avisador avisador;

    ServicioRegistro(ValidadorCorreo validador, RepositorioUsuarios repositorio, Avisador avisador) {
        this.validador = validador;
        this.repositorio = repositorio;
        this.avisador = avisador;
    }

    String registrar(String correo) {
        if (!validador.esValido(correo)) {
            return "correo no válido";
        }
        repositorio.guardar(correo);
        avisador.bienvenida(correo);
        return "registrado";
    }
}

public class Sols {
    public static void main(String[] args) {
        List<String> correos = List.of("ana@correo.es", "correo-malo");

        RegistroMalo malo = new RegistroMalo();
        for (String c : correos) {
            System.out.println("[antes] " + c + " -> " + malo.registrar(c));
        }

        RepositorioUsuarios repositorio = new RepositorioUsuarios();
        Avisador avisador = new Avisador();
        ServicioRegistro servicio = new ServicioRegistro(new ValidadorCorreo(), repositorio, avisador);
        for (String c : correos) {
            System.out.println("[después] " + c + " -> " + servicio.registrar(c));
        }
        System.out.println("usuarios guardados (después): " + repositorio.total());
        System.out.println("avisos enviados (después): " + String.join(", ", avisador.enviados));
    }
}
```

Salida:

```text
[antes] ana@correo.es -> registrado
[antes] correo-malo -> correo no válido
[después] ana@correo.es -> registrado
[después] correo-malo -> correo no válido
usuarios guardados (después): 1
avisos enviados (después): Bienvenido, ana@correo.es
```

* **Antes:** `RegistroMalo` tiene tres responsabilidades. Si cambia la regla de validación, **o** el sitio donde se guardan los usuarios, **o** el texto del aviso, hay que editar la misma clase.
* **Después:** cada clase hace una cosa (`ValidadorCorreo`, `RepositorioUsuarios`, `Avisador`) y `ServicioRegistro` las **coordina**. Un cambio afecta a una sola clase, y cada pieza se puede probar por separado.

**Cómo detectarlo:** si para describir una clase necesitas decir «y» («valida **y** guarda **y** avisa»), probablemente son varias clases.

## O · Abierto/cerrado

El código debería estar **abierto a la ampliación** (se pueden añadir comportamientos nuevos) pero **cerrado a la modificación** (no hace falta tocar lo que ya funciona). Cada vez que se edita código que ya estaba probado, se arriesga a romperlo.

```java
import java.util.List;
import java.util.stream.Collectors;

class Antes {
    // ANTES: añadir un formato obliga a EDITAR este método
    static String exportar(String formato, List<String> datos) {
        if (formato.equals("txt")) {
            return String.join(" ", datos);
        } else if (formato.equals("csv")) {
            return String.join(",", datos);
        } else {
            throw new IllegalArgumentException("formato desconocido: " + formato);
        }
    }
}

// DESPUÉS: añadir un formato es añadir una clase; lo que ya existe no cambia
interface Exportador {
    String nombre();

    String exportar(List<String> datos);
}

class ExportadorTxt implements Exportador {
    public String nombre() {
        return "txt";
    }

    public String exportar(List<String> datos) {
        return String.join(" ", datos);
    }
}

class ExportadorCsv implements Exportador {
    public String nombre() {
        return "csv";
    }

    public String exportar(List<String> datos) {
        return String.join(",", datos);
    }
}

// formato nuevo, escrito sin tocar el código anterior
class ExportadorJson implements Exportador {
    public String nombre() {
        return "json";
    }

    public String exportar(List<String> datos) {
        return datos.stream().map(d -> "\"" + d + "\"").collect(Collectors.joining(",", "[", "]"));
    }
}

public class Solo {
    public static void main(String[] args) {
        List<String> datos = List.of("uno", "dos", "tres");
        for (String formato : List.of("txt", "csv", "json")) {
            try {
                System.out.println("[antes] " + formato + ": " + Antes.exportar(formato, datos));
            } catch (IllegalArgumentException e) {
                System.out.println("[antes] " + formato + ": Error: " + e.getMessage());
            }
        }

        List<Exportador> exportadores = List.of(new ExportadorTxt(), new ExportadorCsv(), new ExportadorJson());
        for (Exportador e : exportadores) {
            System.out.println("[después] " + e.nombre() + ": " + e.exportar(datos));
        }
    }
}
```

Salida:

```text
[antes] txt: uno dos tres
[antes] csv: uno,dos,tres
[antes] json: Error: formato desconocido: json
[después] txt: uno dos tres
[después] csv: uno,dos,tres
[después] json: ["uno","dos","tres"]
```

* **Antes:** para añadir el formato `json` hay que **editar** la función `exportar`. Y así con cada formato nuevo: la cadena de `if` no deja de crecer.
* **Después:** cada formato es una clase que cumple un contrato (`Exportador`). Para añadir `json` se **escribe una clase nueva** y las demás **no se tocan**.

**Cómo detectarlo:** una cadena de `if`/`else if` o un `switch` que cambia según un «tipo» y que hay que ampliar cada vez que aparece uno nuevo. La solución casi siempre combina una **interfaz** ([5.C](../u05/t-interfaces.md)) con **polimorfismo**.

!!! warning "No te pases"
    No hace falta una interfaz por cada `if`. Con dos o tres casos que probablemente no cambiarán, un `if` sencillo es perfectamente válido. Aplica el principio cuando notes que **de verdad** se añaden variantes con frecuencia.

## Para practicar

Haz los ejercicios S y O de [U6.2 · SOLID](solid.md). Y la comparación entre lenguajes de interfaces y funciones está en [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
