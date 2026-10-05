# 1.4 Pruebas con JUnit

Una **prueba unitaria** comprueba automáticamente que un método devuelve lo esperado. En Java se usa **JUnit 5**.

## Dependencia (Maven)

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter</artifactId>
  <version>5.11.0</version>
  <scope>test</scope>
</dependency>
```

## La clase a probar

```java
// src/main/java/Calculadora.java
public class Calculadora {
    public int suma(int a, int b) {
        return a + b;
    }

    public double dividir(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("b no puede ser 0");
        }
        return (double) a / b;
    }
}
```

## La prueba

```java
// src/test/java/CalculadoraTest.java
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.Test;

class CalculadoraTest {
    private final Calculadora calc = new Calculadora();

    @Test
    void sumaDosPositivos() {
        assertEquals(5, calc.suma(2, 3));
    }

    @Test
    void dividirPorCeroLanzaExcepcion() {
        assertThrows(IllegalArgumentException.class, () -> calc.dividir(1, 0));
    }
}
```

```bash
mvn test
```

!!! tip "Patrón AAA"
    **A**rrange (preparar), **A**ct (ejecutar), **A**ssert (comprobar).
