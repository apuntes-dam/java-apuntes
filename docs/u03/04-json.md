# 3.4 JSON

**JSON** (*JavaScript Object Notation*) es un formato de **texto** para guardar e intercambiar datos. Es el formato más usado entre aplicaciones y servidores web, porque es compacto, fácil de leer para una persona y todos los lenguajes saben procesarlo.

```json
{
  "usuarios": [
    {"id": 1, "nombre": "Juan", "edad": 30},
    {"id": 2, "nombre": "Ana", "edad": 25}
  ]
}
```

## Qué contiene un JSON

| En JSON | Ejemplo | En Java es... |
|---|---|---|
| Objeto `{ }` | `{"id": 1}` | un mapa (o un objeto de una clase) |
| Array `[ ]` | `[1, 2, 3]` | una lista |
| Texto | `"Ana"` | una cadena |
| Número | `25` · `3.5` | un entero o decimal |
| Booleano | `true` · `false` | un booleano |
| Nulo | `null` | el valor nulo |

Las reglas son estrictas: las claves y los textos van **siempre entre comillas dobles**, **no se admite una coma al final** de una lista ni de un objeto y **no hay comentarios**. La mayoría de errores con JSON son una de estas tres cosas.

Java **no incluye** un lector de JSON en su librería estándar: hay que usar una librería. Este ejemplo usa **Gson** (de Google); otra muy usada es **Jackson**. Para añadirla a un proyecto Gradle: `implementation("com.google.code.gson:gson:2.11.0")` (en Maven, `com.google.code.gson:gson`). Para probar un archivo suelto desde la terminal: `java -cp gson-2.11.0.jar Json1.java`. Gson convierte el JSON en **objetos de tus propias clases**: los nombres de los campos deben coincidir con las claves.

## Leer, modificar y escribir

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import java.util.List;

class Usuario {
    int id;
    String nombre;
    int edad;

    Usuario(int id, String nombre, int edad) {
        this.id = id;
        this.nombre = nombre;
        this.edad = edad;
    }
}

class Datos {
    List<Usuario> usuarios;
}

public class Json1 {
    static void mostrar(Datos datos) {
        for (Usuario u : datos.usuarios) {
            System.out.println("ID: " + u.id + ", Nombre: " + u.nombre + ", Edad: " + u.edad);
        }
    }

    public static void main(String[] args) {
        String texto = "{\"usuarios\": [{\"id\": 1, \"nombre\": \"Juan\", \"edad\": 30}, "
                + "{\"id\": 2, \"nombre\": \"Ana\", \"edad\": 25}]}";
        Datos datos = new Gson().fromJson(texto, Datos.class);
        mostrar(datos);

        datos.usuarios.get(1).edad = 26;                 // actualizar
        datos.usuarios.add(new Usuario(3, "Eva", 22));   // insertar
        datos.usuarios.removeIf(u -> u.id == 1);         // eliminar

        System.out.println("--- después de los cambios ---");
        mostrar(datos);
        System.out.println(new GsonBuilder().setPrettyPrinting().create().toJson(datos));
    }
}
```

Salida:

```text
ID: 1, Nombre: Juan, Edad: 30
ID: 2, Nombre: Ana, Edad: 25
--- después de los cambios ---
ID: 2, Nombre: Ana, Edad: 26
ID: 3, Nombre: Eva, Edad: 22
{
  "usuarios": [
    {
      "id": 2,
      "nombre": "Ana",
      "edad": 26
    },
    {
      "id": 3,
      "nombre": "Eva",
      "edad": 22
    }
  ]
}
```

El proceso siempre es el mismo en tres pasos:

1. **Convertir el texto** en estructuras del lenguaje (mapas, listas u objetos).
2. **Trabajar con ellas** como con cualquier mapa o lista. Con los datos convertidos a objetos, se trabaja como con cualquier lista de objetos: `get(1).edad = 26`, `add(...)` y `removeIf(...)`.
3. **Convertirlas de nuevo en texto**, con sangría si lo van a leer personas.

## Trabajar con archivos y controlar los errores

Los datos suelen estar en un **archivo**. Al leerlo pueden pasar dos cosas que hay que controlar siempre: que el archivo **no exista** o que su contenido **no sea un JSON válido**. Un JSON mal formado lanza una **`JsonSyntaxException`** (en Gson).

```java
import com.google.gson.Gson;
import com.google.gson.JsonSyntaxException;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;
import java.util.Map;

public class Json2 {
    static List<?> cargar(String ruta) throws IOException {
        Path archivo = Path.of(ruta);
        if (!Files.exists(archivo)) {
            System.out.println("No existe el archivo '" + ruta + "'");
            return null;
        }
        try {
            Map<?, ?> datos = new Gson().fromJson(Files.readString(archivo), Map.class);
            return (List<?>) datos.get("usuarios");
        } catch (JsonSyntaxException e) {
            System.out.println("El archivo '" + ruta + "' no contiene un JSON válido");
            return null;
        }
    }

    public static void main(String[] args) throws IOException {
        cargar("no_existe.json");

        Files.writeString(Path.of("malo.json"), "{ esto no es json");
        cargar("malo.json");

        Files.writeString(Path.of("bueno.json"),
                "{\"usuarios\": [{\"id\": 1, \"nombre\": \"Juan\", \"edad\": 30}, "
                        + "{\"id\": 2, \"nombre\": \"Ana\", \"edad\": 25}]}");
        List<?> usuarios = cargar("bueno.json");
        System.out.println("Cargados " + usuarios.size() + " usuarios");

        Files.delete(Path.of("malo.json"));
        Files.delete(Path.of("bueno.json"));
    }
}
```

Salida:

```text
No existe el archivo 'no_existe.json'
El archivo 'malo.json' no contiene un JSON válido
Cargados 2 usuarios
```

Comprobar la existencia **antes** de abrir y capturar el error de formato permite dar un mensaje claro en vez de que el programa se detenga. Los archivos de texto se guardan en **UTF-8**, la codificación habitual del JSON.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Comillas simples en el texto del JSON | En JSON solo valen las **dobles** |
| Una coma de más al final | Quítala: JSON no la permite |
| Esperar un número y recibir texto (o al revés) | Comprueba el tipo o convierte |
| Pedir una clave que no existe | Comprueba con las consultas seguras del apartado 3.2 |
| Perder las tildes al guardar | Usa UTF-8 en lectura y escritura |

## Para practicar

Haz el [ejercicio 3.4 de JSON](json.md), la gestión de usuarios en un archivo.
