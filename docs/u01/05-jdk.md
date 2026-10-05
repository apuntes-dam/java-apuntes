# 1.5 Compilar y ejecutar: JDK y JVM

## Piezas

| Sigla | Qué es |
|---|---|
| **JDK** | Kit de desarrollo: incluye el compilador `javac` y el JRE |
| **JRE** | Entorno de ejecución: JVM + librerías |
| **JVM** | Máquina virtual que ejecuta el *bytecode* |

## Flujo

```text
Hola.java  --javac-->  Hola.class (bytecode)  --java-->  JVM ejecuta
```

## Primer ejemplo por consola

```java
// Hola.java
public class Hola {
    public static void main(String[] args) {
        System.out.println("Hola, mundo");
    }
}
```

```bash
javac Hola.java   # genera Hola.class
java Hola         # ejecuta (sin la extensión)
```

También, desde Java 11, se puede ejecutar un único archivo directamente:

```bash
java Hola.java
```

## Comprobar la instalación

```bash
java -version
javac -version
```

!!! note "Errores frecuentes"
    * El nombre de la clase pública debe coincidir con el del archivo.
    * `javac` no encontrado: falta el JDK o no está en el `PATH`.
    * Se ejecuta la clase (`java Hola`), no el archivo `.class`.
