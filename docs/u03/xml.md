# U3.5 · XML

<div class="ej-gate" data-unit="u03" data-nombre="U3 · Estructuras de datos"></div>

Leer, modificar y guardar datos en un archivo XML.

## Ejercicio 3.5.1

**Gestión de usuarios en XML.** Parte de este archivo `datos_usuarios_orig.xml`:

```xml
<usuarios>
    <usuario>
        <id>1</id>
        <nombre>Juan</nombre>
        <edad>30</edad>
    </usuario>
    <usuario>
        <id>2</id>
        <nombre>Ana</nombre>
        <edad>25</edad>
    </usuario>
</usuarios>
```

Programa estas funciones:

* `mostrarDatos(raiz)`: muestra `ID: <id>, Nombre: <nombre>, Edad: <edad>` de cada usuario; si no hay, avisa.
* `inicializarDatos()`: copia el archivo `_orig` en `datos_usuarios.xml`, controlando que el origen **no exista** y que sea un **XML inválido**.
* `crearArbol(nombreRaiz)`: devuelve un árbol XML vacío con ese nodo raíz (para cuando el archivo no exista o esté corrupto).
* `cargarXml()` y `guardarXml()`.

El flujo de `main` es el mismo que en el ejercicio de JSON: inicializar, cargar (o crear el árbol si falla), mostrar, y luego actualizar una edad, insertar y eliminar usuarios, guardando al final.

!!! note "En Java"
    Usa DOM: `DocumentBuilderFactory`, `Document` y `Transformer` para guardar. Un XML inválido lanza `SAXParseException`.
