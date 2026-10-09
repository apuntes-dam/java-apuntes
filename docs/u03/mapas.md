# U3.2 · Mapas (diccionarios)

<div class="ej-gate" data-unit="u03" data-nombre="U3 · Estructuras de datos"></div>

Pares clave → valor. En Python se llaman diccionarios; en Dart, Java y Kotlin, mapas (`Map`).

## Ejercicio 3.2.1

Guarda el mapa `{Euro: €, Dollar: $, Yen: ¥}`, pregunta por una divisa y muestra su símbolo, o un aviso si no está.

<details class="sol" data-key="u345/u3-2/3.2.1">
<summary>Solución modelo (bloqueada)</summary>
<div class="sol-body"><p class="sol-aviso">🔒 La solución completa está bloqueada. Esto es solo un ejemplo de cómo empieza:</p><pre><code>import java.util.Map;
import java.util.Scanner;
public class Solucion {
    public static void main(String[] args) {
        Map&lt;String, String&gt; divisas = Map.of("Euro", "€", "Dollar", "$", "Yen", "¥");
// ... (resto de la solución bloqueado)</code></pre></div>
</details>

## Ejercicio 3.2.2

Pregunta nombre, edad, dirección y teléfono, guárdalos en un mapa y muestra `<nombre> tiene <edad> años, vive en <dirección> y su número de teléfono es <teléfono>`.

## Ejercicio 3.2.3

Guarda los precios por kilo (Plátano 1.35, Manzana 0.80, Pera 0.85, Naranja 0.70). Pregunta una fruta y los kilos y muestra el precio. Si la fruta no existe, informa de ello.

## Ejercicio 3.2.4

Pregunta una fecha `dd/mm/aaaa` y muéstrala como `dd de <mes> de aaaa`, con el nombre del mes.

## Ejercicio 3.2.5

Guarda los créditos `{Matemáticas: 6, Física: 4, Química: 5}`, muestra `<asignatura> tiene <créditos> créditos` por cada una y el total de créditos al final.

## Ejercicio 3.2.6

Crea un mapa vacío y llénalo con datos de una persona (nombre, edad, sexo, teléfono, correo...) que pidas al usuario. Tras añadir cada dato, muestra el contenido del mapa.

## Ejercicio 3.2.7

Simula una cesta de la compra: pregunta artículo y precio, añade el par al mapa hasta que el usuario termine y muestra la lista de la compra con el total.

## Ejercicio 3.2.8

Crea un mapa de traducción español→inglés. El usuario escribe los pares como `palabra:traducción` separados por comas. Después pide una frase en español y tradúcela palabra a palabra, dejando sin traducir las que no estén.

## Ejercicio 3.2.9

Gestiona las facturas pendientes de cobro con un mapa (número de factura → importe). Menú: añadir factura, pagar una existente (se elimina) o terminar. Tras cada operación muestra lo cobrado hasta ahora y lo pendiente.

## Ejercicio 3.2.10

Gestiona una base de datos de clientes: mapa NIF → otro mapa con nombre, dirección, teléfono, correo y `preferente` (verdadero/falso). Menú: (1) añadir, (2) eliminar, (3) mostrar un cliente, (4) listar todos, (5) listar preferentes, (6) terminar.

!!! note "En Java"
    El mapa interior mezcla textos y un booleano: usa `Map<String, Object>` (o guarda `preferente` como texto).

## Ejercicio 3.2.11

El directorio de clientes es un texto con una línea por cliente, separadas por salto de línea, y la primera con los nombres de los campos:

```text
nif;nombre;email;teléfono;descuento
01234567L;Luis González;luisgonzalez@mail.com;656343576;12.5
71476342J;Macarena Ramírez;macarena@mail.com;692839321;8
63823376M;Juan José Martínez;juanjo@mail.com;664888233;5.2
98376547F;Carmen Sánchez;carmen@mail.com;667677855;15.7
```

Genera un mapa donde la clave sea el NIF y el valor otro mapa con el resto de campos (el descuento como número decimal).
