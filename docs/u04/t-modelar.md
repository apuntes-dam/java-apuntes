# 4.E Del problema al programa

Saber escribir una clase es la mitad del trabajo; la otra mitad es **decidir qué clases hacen falta**. Un enunciado largo puede asustar, pero hay un método sencillo para empezar.

## Cómo diseñar con objetos

1. **Lee el enunciado y subraya los sustantivos.** Suelen ser las **clases** («cuenta», «robot», «cliente») o sus atributos («saldo», «posición»).
2. **Subraya los verbos.** Suelen ser los **métodos** («ingresar», «avanzar», «calcular el total»).
3. **Asigna cada verbo a quien tiene los datos.** Si el robot sabe dónde está, es el robot quien debe saber `avanzar`.
4. **Decide qué es privado.** Lo que no necesite saber el resto, oculto; las reglas, dentro de la clase que las cumple.
5. **Escribe `main` al final**, solo para crear objetos y hacer que colaboren.

!!! example "Un ejemplo de lectura"
    «Una *tienda* vende *productos* con un *precio*. Un *cliente* llena un *carrito* y calcula el *total*.» → Clases candidatas: `Producto`, `Carrito`, `Cliente`. Atributo de `Producto`: `precio`. Método de `Carrito`: `total`. Pregunta clave: ¿quién sabe el total? Quien tiene los productos: el `Carrito`.

## Del programa con bucles al programa con objetos

Un enfoque habitual es resolver primero el problema **sin objetos** (variables sueltas, bucles y condicionales) y después reorganizarlo: las variables que viajan juntas se convierten en **atributos** de una clase y los trozos de código que las modifican, en **métodos**. Así la solución final hace lo mismo, pero mucho más ordenada y fácil de ampliar.

## Comportamiento que cambia: pasar una función como parámetro

A veces varios objetos de la misma clase se comportan **casi igual**, salvo en un detalle (cómo se calcula un descuento, qué hace al terminar un movimiento). Una forma sencilla de resolverlo es **guardar ese detalle como una función** que se le pasa al objeto al crearlo.

```java
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;

interface Descuento {
    int calcular(int total, int articulos);
}

class Carrito {
    private final List<Integer> precios;
    private final Descuento descuento;

    Carrito(List<Integer> precios, Descuento descuento) {
        this.precios = precios;
        this.descuento = descuento;
    }

    int total() {
        int suma = 0;
        for (int precio : precios) {
            suma += precio;
        }
        return suma - descuento.calcular(suma, precios.size());
    }
}

public class Poo5 {
    public static void main(String[] args) {
        List<Integer> precios = List.of(30, 40, 30);
        Map<String, Descuento> reglas = new LinkedHashMap<>();
        reglas.put("Sin descuento", (total, n) -> 0);
        reglas.put("Descuento fijo de 10", (total, n) -> 10);
        reglas.put("Descuento del 20 %", (total, n) -> total * 20 / 100);
        reglas.put("Por volumen (3 o más artículos, 15 %)", (total, n) -> n >= 3 ? total * 15 / 100 : 0);
        reglas.forEach((texto, regla) ->
                System.out.println(texto + ": " + new Carrito(precios, regla).total()));
    }
}
```

Salida:

```text
Sin descuento: 100
Descuento fijo de 10: 90
Descuento del 20 %: 80
Por volumen (3 o más artículos, 15 %): 85
```

La clase `Carrito` es **una sola**, pero recibe la regla del descuento como parámetro: cuatro reglas distintas, ninguna cadena de `if` y ninguna clase nueva. Si mañana hay una regla más, solo hay que escribirla; `Carrito` no cambia.

En Java se define una **interfaz con un único método** (`interface Descuento`) y se pasa una **expresión lambda** que la implementa: `(total, n) -> total * 20 / 100`. También existen interfaces ya hechas, como `Function` y `BiFunction`.

!!! info "Más adelante"
    Cuando las variantes necesitan **varios métodos** distintos y no solo un cálculo, se usan **interfaces y herencia**, que se ven en la [unidad 5](../u05/index.md).

## Señales de un mal diseño

| Síntoma | Qué probar |
|---|---|
| Una clase enorme que hace de todo | Dividirla: cada clase, una responsabilidad |
| Clases que solo guardan datos y toda la lógica está en `main` | Mover las operaciones a los métodos de la clase dueña de los datos |
| Atributos públicos que todo el mundo modifica | Encapsularlos (4.B) |
| El mismo `if` repetido por varios sitios según un «tipo» | Un enumerado con datos, o pasar el comportamiento como función |
| Un método con muchos parámetros que son siempre los mismos | Agruparlos en un objeto |

## Para practicar

Los ejercicios de robots ([U4.4](robots-1.md) y [U4.5](robots-2.md)) practican justo esto: partir de un programa con bucles, pasarlo a objetos y dar a cada robot su propio comportamiento. Para un proyecto completo, mira el [reto personal](proyecto.md). [Pasar de uno a otro](https://apuntes-dam.github.io/apuntes-lenguajes/pasar/) compara el código de estas ideas entre lenguajes.
