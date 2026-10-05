# U4.7 · Reto personal: gestor de inventario

<div class="ej-gate" data-unit="u04" data-nombre="U4 · Programación orientada a objetos"></div>

Proyecto final de POO para Java. Sustituye al juego del ahorcado: repasa casi todo lo aprendido, sin ser enorme. Piénsalo como un **reto personal**, no como un examen.

## Ejercicio 4.16

**Gestor de inventario de una tienda.** Programa en consola el inventario de una tienda. Cada nivel se puede entregar por separado: empieza por el básico y sube cuando te apetezca.

**Piezas mínimas**

* `Producto` (clase abstracta): código, nombre, precio base, stock y el método abstracto `precioFinal()`.
* Subclases: `ProductoFisico` (peso; suma gastos de envío), `ProductoDigital` (sin stock físico) y `Perecedero` (fecha de caducidad; rebaja el precio si caduca en menos de 3 días).
* `Inventario`: un `Map<String, Producto>` indexado por código.
* `Venta`: un `record` con producto, unidades, total y fecha.

**Nivel básico**

1. Menú de consola: añadir producto, eliminar, buscar por código (devuelve un `Optional`), listar y salir.
2. Vender unidades de un producto y actualizar el stock.
3. Mostrar las ventas realizadas.

**Nivel medio**

4. Excepciones propias: `ProductoNoEncontrado` y `StockInsuficiente`.
5. Una interfaz `Descuento` con `double aplicar(double precio)` e implementaciones: porcentaje, importe fijo y 2x1. Asocia un descuento opcional a cada producto.
6. Un `enum Categoria` y listado de productos por categoría.

**Reto extra (opcional)**

7. Informes con *streams*: los 3 productos más vendidos, ingresos totales y productos con stock bajo (menos de 5), ordenados por stock.
8. Guarda y carga el inventario en un archivo CSV.
9. Escribe pruebas con JUnit para `Inventario` y para el cálculo de precios.

**Qué repasas**

| Concepto | Dónde aparece |
|---|---|
| Herencia y clases abstractas | `Producto` y sus subclases |
| Polimorfismo | `precioFinal()` en una lista de productos |
| Interfaces | `Descuento` |
| `record` y `enum` | `Venta`, `Categoria` |
| Colecciones | `Map`, `List`, `Set` |
| Excepciones propias | `ProductoNoEncontrado`, `StockInsuficiente` |
| `Optional` y *streams* | búsqueda e informes |
| Entrada/salida y JUnit | CSV y pruebas (reto extra) |

*Consejo: dibuja antes la jerarquía de clases en papel y empieza por `Producto` y `Inventario`. Añade los descuentos al final: así ves lo que aporta una interfaz.*
