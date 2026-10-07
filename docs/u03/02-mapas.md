# 3.2 Mapas (diccionarios)

Un **mapa** (o **diccionario**) guarda **pares clave → valor**. En lugar de buscar por posición, como en una lista, se busca por clave: dado un nombre, su edad; dada una palabra, cuántas veces aparece. Las **claves son únicas**: si asignas otra vez la misma clave, se sobrescribe el valor.

En Java es `Map<K, V>` y hay que elegir una implementación: **`HashMap`** (rápida, **sin orden garantizado**), **`LinkedHashMap`** (recuerda el orden de inserción, como en el ejemplo) o **`TreeMap`** (ordenada por clave). Si imprimes un `HashMap` el orden puede sorprenderte.

## Crear, consultar, modificar y borrar

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class Map1 {
    public static void main(String[] args) {
        Map<String, Integer> edades = new LinkedHashMap<>();
        edades.put("Ana", 25);
        edades.put("Luis", 30);
        System.out.println("edad de Ana: " + edades.get("Ana"));
        System.out.println("Pepe: " + (edades.containsKey("Pepe") ? edades.get("Pepe") : "no está"));
        System.out.println("Pepe con valor por defecto: " + edades.getOrDefault("Pepe", 0));

        edades.put("Eva", 22); // añadir
        edades.put("Ana", 26); // modificar
        for (Map.Entry<String, Integer> entrada : edades.entrySet()) {
            System.out.println(entrada.getKey() + " -> " + entrada.getValue());
        }
        edades.remove("Luis");
        System.out.println("tras borrar a Luis quedan " + edades.size());

        Map<String, Integer> cuentas = new LinkedHashMap<>();
        for (String palabra : "a b a c b a".split(" ")) {
            cuentas.merge(palabra, 1, Integer::sum);
        }
        System.out.println("frecuencias:");
        cuentas.forEach((clave, valor) -> System.out.println(clave + " -> " + valor));
    }
}
```

Salida:

```text
edad de Ana: 25
Pepe: no está
Pepe con valor por defecto: 0
Ana -> 26
Luis -> 30
Eva -> 22
tras borrar a Luis quedan 2
frecuencias:
a -> 3
b -> 2
c -> 1
```

| Operación | En Java |
|---|---|
| Consultar (`null` si no está) | `edades.get("Ana")` |
| Valor por defecto | `edades.getOrDefault("Pepe", 0)` |
| Añadir o modificar | `edades.put("Eva", 22)` |
| ¿Existe la clave? | `edades.containsKey("Ana")` |
| Borrar | `edades.remove("Luis")` |
| Tamaño | `edades.size()` |
| Solo claves · solo valores | `edades.keySet()` · `edades.values()` |
| Recorrer pares | `for (Map.Entry<K, V> e : edades.entrySet())` → `getKey()`, `getValue()` |

!!! warning "Consultar una clave que no existe"
    Consultar una clave que no existe **no falla**: `get` devuelve `null`, y si lo usas sin comprobar, tendrás un `NullPointerException` más adelante. Usa `containsKey` o `getOrDefault`.

## Contar con un mapa

La segunda mitad del ejemplo es un patrón que se usa constantemente: **contar cuántas veces aparece cada elemento**. Para cada palabra, se lee su cuenta actual (o 0 si es la primera vez), se suma 1 y se guarda de nuevo.

## Claves, valores y mapas de listas

```java
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;

public class Map2 {
    public static void main(String[] args) {
        Map<String, List<Integer>> notas = new LinkedHashMap<>();
        notas.put("Ana", List.of(7, 9));
        notas.put("Luis", List.of(5, 6, 7));

        System.out.println("claves: " + String.join(", ", notas.keySet()));
        for (Map.Entry<String, List<Integer>> entrada : notas.entrySet()) {
            List<Integer> lista = entrada.getValue();
            int suma = 0;
            for (int n : lista) {
                suma += n;
            }
            System.out.println(entrada.getKey() + ": media " + (double) suma / lista.size());
        }
        System.out.println("total de notas: " + notas.values().stream().mapToInt(List::size).sum());
    }
}
```

Salida:

```text
claves: Ana, Luis
Ana: media 8.0
Luis: media 6.0
total de notas: 5
```

Los valores pueden ser de cualquier tipo, incluidas **listas** u otros mapas. Aquí cada alumno tiene una lista de notas; se recorre el mapa y, para cada par, se recorre su lista.

## ¿Mapa, lista o conjunto?

| Necesito... | Estructura |
|---|---|
| Datos ordenados a los que accedo por posición | Lista |
| Buscar un dato a partir de otro (nombre → edad) | **Mapa** |
| Saber si algo está, sin repeticiones | Conjunto |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Usar el valor de una clave que no existe | Comprueba, o usa el valor por defecto |
| Esperar un orden concreto | Usa una versión ordenada si el orden importa |
| Modificar el mapa mientras lo recorres | Recorre una copia de las claves o construye un mapa nuevo |
| Repetir una clave pensando que añade otra entrada | Las claves son únicas: la segunda **sobrescribe** |

## Para practicar

Haz los [ejercicios 3.2 de mapas](mapas.md). Y para ver cómo se escribe en otro lenguaje: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
