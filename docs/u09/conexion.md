# U9.1 · Conexión y creación de tablas

<div class="ej-gate" data-unit="u09" data-nombre="U9 · Bases de datos relacionales"></div>

Bases de datos relacionales desde el código: conectar, crear tablas e insertar datos controlando los errores.

## Ejercicio 9.1

**Conexión.** Prepara una base de datos en **modo fichero** (por ejemplo `./data/tienda`) y escribe un programa que se conecte a ella controlando los errores (`try`/`catch`) y cierre siempre la conexión. Muestra un mensaje de éxito o un mensaje claro si falla.

!!! note "En Java"
    Usa **H2** en modo fichero (`jdbc:h2:./data/tienda`) con `DriverManager.getConnection`. Añade la dependencia `com.h2database:h2` y usa *try-with-resources*.

## Ejercicio 9.2

**Tablas e inserciones.** Crea las tablas del siguiente modelo (usuarios → pedidos → líneas de pedido ← productos) y rellénalas desde tu programa, usando sentencias parametrizadas.

```sql
CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  email VARCHAR(150) UNIQUE
);
CREATE TABLE productos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  precio DECIMAL(10,2) NOT NULL,
  stock INT NOT NULL
);
CREATE TABLE pedidos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  id_usuario INT NOT NULL,
  total DECIMAL(10,2) NOT NULL,
  FOREIGN KEY (id_usuario) REFERENCES usuarios(id)
);
CREATE TABLE lineas_pedido (
  id INT AUTO_INCREMENT PRIMARY KEY,
  id_pedido INT NOT NULL,
  id_producto INT NOT NULL,
  cantidad INT NOT NULL,
  precio DECIMAL(10,2) NOT NULL,
  FOREIGN KEY (id_pedido) REFERENCES pedidos(id),
  FOREIGN KEY (id_producto) REFERENCES productos(id)
);
```

**Datos a insertar** (los identificadores son autonuméricos: no los incluyas):

| usuarios: nombre | email |
|---|---|
| Lola Marín | lola@mail.com |
| Pablo Gil | pablo@mail.com |
| Rocío Vela | rocio@mail.com |

| productos: nombre | precio (€) | stock |
|---|---|---|
| Teclado | 25 | 10 |
| Ratón | 12.50 | 30 |
| Monitor | 140 | 3 |

| pedidos: id_usuario | total (€) |
|---|---|
| 2 | 165 |
| 1 | 25 |
| 2 | 12.50 |

| lineas_pedido: id_pedido | id_producto | cantidad | precio (€) |
|---|---|---|---|
| 1 | 3 | 1 | 140 |
| 1 | 1 | 1 | 25 |
| 2 | 1 | 1 | 25 |
| 3 | 2 | 1 | 12.50 |

Maneja los errores de cada inserción y comprueba que, si repites un email, la base de datos lo rechaza.
