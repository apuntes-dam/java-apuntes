# 3.0 Cadenas de texto

Una **cadena** es una secuencia de caracteres: un nombre, una frase, el contenido de un archivo. Casi todo programa las maneja, y casi siempre se hace lo mismo: **recorrerlas, buscar dentro, partirlas y transformarlas**.

En Java el texto es un `String`. Las cadenas son **inmutables**: ningún método modifica la original, todos **devuelven una nueva**.

## Recorrer y buscar

```java
public class Cad1 {
    public static void main(String[] args) {
        String texto = "banana";
        System.out.println("longitud: " + texto.length());
        System.out.println("primera: " + texto.charAt(0) + ", última: " + texto.charAt(texto.length() - 1));
        System.out.println("subcadena: " + texto.substring(2, 5));

        int cuenta = 0;
        for (int i = 0; i < texto.length(); i++) {
            if (texto.charAt(i) == 'a') {
                cuenta++;
            }
        }
        System.out.println("letras a: " + cuenta);
        System.out.println("posición de \"na\": " + texto.indexOf("na"));
        System.out.println("contiene \"nan\": " + (texto.contains("nan") ? "sí" : "no"));

        StringBuilder alReves = new StringBuilder();
        int j = texto.length() - 1;
        while (j >= 0) {
            alReves.append(texto.charAt(j));
            j--;
        }
        System.out.println("al revés: " + alReves);
    }
}
```

Salida:

```text
longitud: 6
primera: b, última: a
subcadena: nan
letras a: 3
posición de "na": 2
contiene "nan": sí
al revés: ananab
```

Los índices empiezan en **0** y el último es `length() - 1`. Acceder fuera de rango lanza `StringIndexOutOfBoundsException`.

Fíjate en el patrón de **recorrido**: una variable que va de la primera posición a la última (o al revés) y, dentro, una pregunta sobre la letra actual. Con él se cuenta, se busca y se invierte; es la base de los ejercicios de este apartado. Las cadenas ya traen métodos que lo hacen por ti (`indexOf`/`find`, `contains`...), pero conviene saber hacerlo a mano.

!!! warning "El final de una subcadena no se incluye"
    `substring(2, 5)` (o `texto[2:5]`) toma las posiciones **2, 3 y 4**. Calcula el tamaño restando: `5 - 2 = 3` letras.

## Transformar

```java
public class Cad2 {
    public static void main(String[] args) {
        String original = "  Hola, Mundo DAM  ";
        String limpio = original.strip();
        System.out.println("recortado: [" + limpio + "]");
        System.out.println("mayúsculas: " + limpio.toUpperCase());
        System.out.println("minúsculas: " + limpio.toLowerCase());
        System.out.println("reemplazado: " + limpio.replace("Mundo", "Clase"));

        String[] partes = limpio.split(", ");
        System.out.println("partes: " + partes.length + " -> " + partes[0] + " | " + partes[1]);
        System.out.println("unido: " + String.join("-", partes));
        System.out.println("empieza por \"Hola\": " + (limpio.startsWith("Hola") ? "sí" : "no"));
        System.out.println("con ceros: " + String.format("%03d", 7));
        System.out.println("original sigue igual: [" + original + "]");
    }
}
```

Salida:

```text
recortado: [Hola, Mundo DAM]
mayúsculas: HOLA, MUNDO DAM
minúsculas: hola, mundo dam
reemplazado: Hola, Clase DAM
partes: 2 -> Hola | Mundo DAM
unido: Hola-Mundo DAM
empieza por "Hola": sí
con ceros: 007
original sigue igual: [  Hola, Mundo DAM  ]
```

Observa la última línea: tras todos los cambios, **`original` no ha variado**, porque cada método devuelve una cadena nueva. Si quieres quedarte con el resultado, guárdalo en una variable (`limpio = original.strip()`), no basta con llamar al método.

## Los métodos más útiles

| Operación | En Java |
|---|---|
| Longitud | `texto.length()` |
| Un carácter | `texto.charAt(i)` |
| Subcadena (el final no se incluye) | `texto.substring(2, 5)` |
| Buscar posición (`-1` si no está) | `texto.indexOf("na")` |
| ¿Contiene? ¿Empieza? ¿Acaba? | `contains("nan")` · `startsWith("ba")` · `endsWith("na")` |
| Reemplazar | `replace("a", "o")` |
| Trocear / unir | `texto.split(", ")` · `String.join("-", partes)` |
| Quitar espacios de los extremos | `strip()` (o `trim()`) |
| Mayúsculas / minúsculas | `toUpperCase()` · `toLowerCase()` |
| Rellenar con ceros | `String.format("%03d", 7)` |
| Repetir | `"ab".repeat(3)` |

Para darle la vuelta rápidamente: `new StringBuilder(texto).reverse().toString()`. Al construir texto en un bucle, usa **`StringBuilder`** (como en el ejemplo) en lugar de `+=`: cada `+=` crea un `String` nuevo y es lento si se repite muchas veces. Para comparar el contenido de dos cadenas usa `equals`, no `==` (ver [2.1](../u02/01-condicionales.md)).

## Unicode

`length()` cuenta **unidades UTF-16**, no «letras» que ve el usuario: un emoji ocupa 2. Para contar caracteres Unicode completos existe `codePointCount`.

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Llamar a un método y esperar que cambie la cadena | Recoge el valor devuelto en una variable |
| Pasarse un puesto al recorrer (`<=` en lugar de `<`) | El último índice es la longitud menos 1 |
| Olvidar que el final de una subcadena no se incluye | Cuenta las letras: `final - inicio` |
| Comparar mayúsculas con minúsculas | Pasa los dos textos a minúsculas antes de comparar |

## Para practicar

Haz los [ejercicios 3.0 de cadenas](cadenas.md). Para ver cómo se escribe lo mismo en otro lenguaje: [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/).
