# 3.5 XML

**XML** (*eXtensible Markup Language*) es otro formato de texto para guardar datos **con estructura de árbol**. Es más antiguo y más verboso que JSON, pero sigue muy presente en configuraciones, documentos, servicios web antiguos y en el propio Android.

```xml
<usuarios>
  <usuario>
    <id>1</id>
    <nombre>Juan</nombre>
    <edad>30</edad>
  </usuario>
</usuarios>
```

## Partes de un XML

| Parte | Qué es | Ejemplo |
|---|---|---|
| **Elemento** | Una etiqueta de apertura, su contenido y la de cierre | `<nombre>Juan</nombre>` |
| **Atributo** | Un dato dentro de la etiqueta de apertura | `<usuario id="1">` |
| **Texto** | El contenido de un elemento | `Juan` |
| **Raíz** | El elemento que contiene a todos los demás (solo hay **uno**) | `<usuarios>` |

Para que un XML sea **válido** (*bien formado*): hay una sola raíz, cada etiqueta que se abre **se cierra** en el orden correcto, los atributos van entre comillas y se distingue entre mayúsculas y minúsculas. Los caracteres especiales se escriben con entidades: `&lt;` (`<`), `&gt;` (`>`) y `&amp;` (`&`).

Java incluye el estándar **DOM** (`javax.xml.parsers`, `org.w3c.dom`): el documento se carga como un **árbol** de nodos que se puede recorrer y modificar. Es potente pero **verboso**. Para escribir el resultado se usa un `Transformer`.

## Leer, modificar y escribir

```java
import java.io.StringReader;
import java.io.StringWriter;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.NodeList;
import org.xml.sax.InputSource;

public class Xml1 {
    static String texto(Element padre, String etiqueta) {
        return padre.getElementsByTagName(etiqueta).item(0).getTextContent();
    }

    static void mostrar(Element raiz) {
        NodeList usuarios = raiz.getElementsByTagName("usuario");
        for (int i = 0; i < usuarios.getLength(); i++) {
            Element u = (Element) usuarios.item(i);
            System.out.println("ID: " + texto(u, "id") + ", Nombre: " + texto(u, "nombre")
                    + ", Edad: " + texto(u, "edad"));
        }
    }

    public static void main(String[] args) throws Exception {
        String xml = "<usuarios><usuario><id>1</id><nombre>Juan</nombre><edad>30</edad></usuario>"
                + "<usuario><id>2</id><nombre>Ana</nombre><edad>25</edad></usuario></usuarios>";
        Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder()
                .parse(new InputSource(new StringReader(xml)));
        Element raiz = doc.getDocumentElement();
        mostrar(raiz);

        NodeList usuarios = raiz.getElementsByTagName("usuario");
        for (int i = 0; i < usuarios.getLength(); i++) {
            Element u = (Element) usuarios.item(i);
            if (texto(u, "nombre").equals("Ana")) {
                u.getElementsByTagName("edad").item(0).setTextContent("26");   // actualizar
            }
        }

        Element nuevo = doc.createElement("usuario");                          // insertar
        String[][] campos = {{"id", "3"}, {"nombre", "Eva"}, {"edad", "22"}};
        for (String[] campo : campos) {
            Element hijo = doc.createElement(campo[0]);
            hijo.setTextContent(campo[1]);
            nuevo.appendChild(hijo);
        }
        raiz.appendChild(nuevo);

        usuarios = raiz.getElementsByTagName("usuario");                       // eliminar
        for (int i = 0; i < usuarios.getLength(); i++) {
            Element u = (Element) usuarios.item(i);
            if (texto(u, "id").equals("1")) {
                raiz.removeChild(u);
                break;
            }
        }

        System.out.println("--- después de los cambios ---");
        mostrar(raiz);

        Transformer t = TransformerFactory.newInstance().newTransformer();
        t.setOutputProperty(OutputKeys.OMIT_XML_DECLARATION, "yes");
        StringWriter salida = new StringWriter();
        t.transform(new DOMSource(raiz), new StreamResult(salida));
        System.out.println(salida);
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
<usuarios><usuario><id>2</id><nombre>Ana</nombre><edad>26</edad></usuario><usuario><id>3</id><nombre>Eva</nombre><edad>22</edad></usuario></usuarios>
```

La idea es la misma que con JSON: cargar el texto como un **árbol**, buscar los elementos que interesan (`usuario`, y dentro `id`, `nombre`, `edad`), modificar el árbol y volver a convertirlo en texto. El texto de entrada de este ejemplo está en **una sola línea** para que la salida sea idéntica en los cuatro lenguajes; en un archivo real lo normal es escribirlo con sangría.

## Crear un árbol, atributos y errores

```java
import java.io.StringReader;
import java.io.StringWriter;
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;
import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.xml.sax.InputSource;
import org.xml.sax.SAXException;

public class Xml2 {
    public static void main(String[] args) throws Exception {
        DocumentBuilder constructor = DocumentBuilderFactory.newInstance().newDocumentBuilder();
        Document doc = constructor.newDocument();
        Element raiz = doc.createElement("usuarios");
        doc.appendChild(raiz);
        Element usuario = doc.createElement("usuario");
        usuario.setAttribute("id", "1");
        usuario.setTextContent("Juan");
        raiz.appendChild(usuario);

        Transformer t = TransformerFactory.newInstance().newTransformer();
        t.setOutputProperty(OutputKeys.OMIT_XML_DECLARATION, "yes");
        StringWriter salida = new StringWriter();
        t.transform(new DOMSource(raiz), new StreamResult(salida));
        System.out.println(salida);
        System.out.println("atributo id: " + usuario.getAttribute("id"));

        try {
            constructor.parse(new InputSource(new StringReader("<usuarios><usuario></usuarios>")));
        } catch (SAXException e) {
            System.out.println("El texto no es un XML válido");
        }
    }
}
```

Salida:

```text
<usuarios><usuario id="1">Juan</usuario></usuarios>
atributo id: 1
El texto no es un XML válido
```

Este ejemplo muestra tres cosas: **crear** un árbol desde cero (un XML vacío con su raíz es el punto de partida cuando un archivo no existe), **leer un atributo** (`id`) y **detectar un XML inválido**. Un XML mal formado lanza una **`SAXException`** (concretamente `SAXParseException`); el analizador además escribe el aviso en la salida de errores.

## JSON o XML

| | JSON | XML |
|---|---|---|
| Aspecto | Compacto | Más verboso (etiquetas de apertura y cierre) |
| Estructura | Objetos y arrays | Árbol de elementos, con atributos |
| Comentarios | No | Sí |
| Uso típico | APIs web, configuración | Documentos, configuraciones, Android |

## Errores frecuentes

| Error | Cómo evitarlo |
|---|---|
| Olvidar cerrar una etiqueta o cerrarla en otro orden | Comprueba que el XML sea válido antes de procesarlo |
| Más de un elemento raíz | Un documento tiene **una** sola raíz |
| `&` o `<` sueltos dentro del texto | Escríbelos como `&amp;` y `&lt;` |
| Asumir que un elemento existe | Comprueba que el resultado de la búsqueda no sea nulo |

## Para practicar

Haz el [ejercicio 3.5 de XML](xml.md), igual que el de JSON pero con un árbol de elementos.
