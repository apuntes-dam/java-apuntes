# U7.2 · Archivos y carpetas

<div class="ej-gate" data-unit="u07" data-nombre="U7 · Entrada/salida y GUI"></div>

Consultar rutas, listar carpetas y crear o borrar ficheros y directorios.

## Ejercicio 7.4

**Información de una ruta.** Pide una ruta y muestra: si existe, si es archivo o carpeta, su nombre, su ruta absoluta, su carpeta padre, su tamaño (si es archivo) y su fecha de última modificación. Si no existe, indícalo sin que el programa falle.

!!! note "En Java"
    Usa `java.nio.file.Path`, `Files` (`exists`, `isDirectory`, `size`, `getLastModifiedTime`; ver [7.E](t-path.md)) o `java.io.File`.

## Ejercicio 7.5

**Listar una carpeta.** Pide una carpeta y lista su contenido mostrando, por cada elemento, si es archivo o carpeta y su tamaño en bytes. Ordena primero las carpetas y después los archivos, ambos por nombre. Al final muestra cuántos elementos hay y el tamaño total de los archivos.

## Ejercicio 7.6

**Mini explorador.** Menú con: crear carpeta, crear archivo vacío (si existe, pregunta antes de vaciarlo), copiar archivo, mover/renombrar y eliminar (con confirmación). Controla las rutas que no existan y los errores de permisos con mensajes claros.
