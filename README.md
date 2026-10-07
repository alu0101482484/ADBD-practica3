# Modelo entidad/relación — Viveros Tajinaste S.A.

**Asignatura:** Administración y Diseño de Bases de Datos — Grado en Ingeniería Informática (ULL)

**Autor:** Claudia Díaz González

**ALU:** alu0101482484

## Contenido del repositorio

| Fichero | Descripción |
|---|---|
| `viveros.drawio` | Modelo entidad/relación editable en [draw.io](https://app.diagrams.net) |
| `viveros.png` | Imagen del modelo entidad/relación |
| `README.md` | Este documento |


---

## 1. Entidades

| Entidad | Tipo | Descripción |
|---|---|---|
| **VIVERO** | Fuerte | Cada uno de los viveros de la red de Tajinaste S.A. |
| **ZONA** | Débil (en identificación respecto de VIVERO) | Espacio físico dentro de un vivero donde se ubican productos y trabajan empleados (zona exterior, almacén, invernadero…). El código de una zona solo es único dentro de su vivero: puede existir un "almacén" en cada vivero. |
| **PRODUCTO** | Fuerte | Artículo que comercializa la empresa: plantas, productos de jardinería o decoración. |
| **EMPLEADO** | Fuerte | Persona que trabaja para la empresa y que es destinada a viveros y zonas según la época del año. |
| **CLIENTE** | Fuerte | Cliente de la empresa. |
| **CLIENTE PLUS** | Subtipo de CLIENTE | Cliente que pertenece al programa de fidelización *Tajinaste Plus*. Solo de estos clientes se registran pedidos y bonificaciones. |
| **PEDIDO** | Fuerte | Pedido realizado por un cliente *Tajinaste Plus* y gestionado por un único empleado responsable. |
| **BONIFICACIÓN** | Débil (en identificación respecto de CLIENTE PLUS) | Bonificación mensual asignada a un cliente *Tajinaste Plus* en función de su volumen de compras de ese mes. |

---

## 2. Atributos y dominios

### VIVERO

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| Código vivero | Identificador | Cadena alfanumérica de hasta 10 caracteres | `VIV-LL01` |
| Nombre | Descriptor | Cadena de hasta 60 caracteres | `Vivero La Laguna` |
| Teléfono | Descriptor | Cadena de 9 dígitos, opcionalmente con prefijo internacional | `922123456` |
| Georreferenciación | Compuesto | (Latitud, Longitud) | `(28.4874, -16.3159)` |
| └ Latitud | Simple | Decimal en [-90, 90], 6 decimales | `28.487400` |
| └ Longitud | Simple | Decimal en [-180, 180], 6 decimales | `-16.315900` |

### ZONA

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| Código zona | Discriminante | Entero positivo, único dentro del vivero | `1`, `2`, `3` |
| Tipo | Descriptor | Conjunto {`exterior`, `interior`, `almacén`, `invernadero`, `exposición`} | `almacén` |
| Georreferenciación | Compuesto | (Latitud, Longitud), mismos dominios que en VIVERO | `(28.4876, -16.3161)` |

La clave completa de una zona es **(Código vivero, Código zona)**. Por ejemplo, `(VIV-LL01, 2)`.

### PRODUCTO

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| Código producto | Identificador | Cadena alfanumérica de hasta 15 caracteres | `PLT-00125` |
| Nombre | Descriptor | Cadena de hasta 80 caracteres | `Drago canario (maceta 20 cm)` |
| Categoría | Descriptor | Conjunto {`planta`, `jardinería`, `decoración`} | `planta` |
| Precio | Descriptor | Decimal ≥ 0 en euros, 2 decimales | `24.95` |

### EMPLEADO

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| DNI | Identificador | 8 dígitos y una letra | `45123456K` |
| Nombre | Descriptor | Cadena de hasta 40 caracteres | `Carmen` |
| Apellidos | Descriptor | Cadena de hasta 80 caracteres | `Hernández Pérez` |
| Email | Descriptor | Dirección de correo válida | `carmen.hernandez@tajinaste.es` |
| Fecha contratación | Descriptor | Fecha (`AAAA-MM-DD`) | `2023-03-01` |

### CLIENTE

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| DNI | Identificador | 8 dígitos y una letra | `78901234Z` |
| Nombre | Descriptor | Cadena de hasta 40 caracteres | `Javier` |
| Apellidos | Descriptor | Cadena de hasta 80 caracteres | `González Díaz` |
| Email | Descriptor | Dirección de correo válida | `javi.gd@gmail.com` |
| Teléfono | Descriptor | Cadena de 9 dígitos | `678123456` |

### CLIENTE PLUS

Hereda todos los atributos de CLIENTE, incluido el identificador DNI.

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| Fecha ingreso | Descriptor | Fecha (`AAAA-MM-DD`) de alta en el programa | `2024-06-15` |

### PEDIDO

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| Nº pedido | Identificador | Entero positivo autoincremental | `10234` |
| Fecha | Descriptor | Fecha (`AAAA-MM-DD`) | `2026-09-20` |
| Importe total | Derivado | Decimal ≥ 0 en euros. Se calcula como Σ (Cantidad × Precio unitario) de las líneas del pedido | `74.85` |

### BONIFICACIÓN

| Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|
| Mes | Discriminante | Año y mes (`AAAA-MM`) | `2026-08` |
| Volumen compras | Derivado | Decimal ≥ 0 en euros. Suma del importe total de los pedidos del cliente en ese mes | `312.40` |
| Importe | Descriptor | Decimal ≥ 0 en euros, bonificación concedida | `15.62` |

La clave completa de una bonificación es **(DNI cliente, Mes)**. Por ejemplo, `(78901234Z, 2026-08)`.

### Atributos propios de las relaciones

| Relación | Atributo | Tipo | Dominio | Ejemplo |
|---|---|---|---|---|
| almacena | Stock | Descriptor | Entero ≥ 0 (unidades disponibles del producto en la zona) | `35` |
| trabaja en | Fecha inicio | Identificador de la relación | Fecha (`AAAA-MM-DD`) | `2026-06-01` |
| trabaja en | Fecha fin | Descriptor | Fecha (`AAAA-MM-DD`), o nulo si es el puesto actual | `2026-09-30` / `NULL` |
| trabaja en | Tarea | Descriptor | Conjunto {`riego`, `poda`, `atención al cliente`, `reposición`, `caja`, `logística`…} | `reposición` |
| incluye | Cantidad | Descriptor | Entero > 0 | `3` |
| incluye | Precio unitario | Descriptor | Decimal ≥ 0 en euros. Precio aplicado en el momento del pedido | `24.95` |

---

## 3. Relaciones y cardinalidades

### 3.1 tiene (VIVERO – ZONA) · dependencia en identificación · **1:N**

`VIVERO (1,1) — ID tiene — (1,N) ZONA`

- Un vivero tiene **como mínimo 1 y como máximo N** zonas, porque todo vivero tiene al menos una zona donde ubicar productos.
- Una zona pertenece **exactamente a 1** vivero.
- ZONA no puede identificarse por sí sola: su código solo es único dentro de su vivero. Si desaparece el vivero, sus zonas carecen de sentido.

### 3.2 almacena (ZONA – PRODUCTO) · **N:M** · atributo propio: *Stock*

`ZONA (0,N) — almacena — (0,N) PRODUCTO`

- En una zona puede haber **de 0 a N** productos asignados. Una zona recién creada puede estar vacía.
- Un producto puede estar asignado a **de 0 a N** zonas, de uno o varios viveros. Un producto nuevo del catálogo puede no estar aún en ninguna zona.
- El stock depende a la vez del producto y de la zona, por eso es atributo de la relación. Responde a "cuánto hay disponible de cada producto en cada zona en la que esté asignado".

### 3.3 trabaja en (ZONA – EMPLEADO) · **N:M** · atributos propios: *Fecha inicio*, *Fecha fin*, *Tarea*

`ZONA (0,N) — trabaja en — (0,N) EMPLEADO`

- A lo largo del tiempo, un empleado puede haber trabajado en **de 0 a N** zonas. Un empleado recién contratado puede no tener destino aún.
- En una zona pueden haber trabajado **de 0 a N** empleados.
- Cada ocurrencia de la relación es un **puesto**: una zona concreta, durante un periodo concreto, desempeñando una tarea. El conjunto de ocurrencias forma el **histórico de puestos** que pide el enunciado. Con él se puede medir la productividad de cada zona a lo largo del tiempo y de cada empleado.
- El vivero de destino no se almacena aparte, porque se deduce de la zona, que pertenece a un único vivero.
- Un mismo empleado puede volver a la misma zona en otra época. Por eso **Fecha inicio forma parte de la identificación** de la relación: (Código vivero, Código zona, DNI empleado, Fecha inicio).

### 3.4 gestiona (EMPLEADO – PEDIDO) · **1:N**

`EMPLEADO (1,1) — gestiona — (0,N) PEDIDO`

- Un empleado gestiona **de 0 a N** pedidos. No todos los empleados atienden pedidos.
- Cada pedido tiene **exactamente 1** empleado responsable, como indica el enunciado.
- Permite medir la capacidad de cada empleado para lograr objetivos de venta.

### 3.5 incluye (PEDIDO – PRODUCTO) · **N:M** · atributos propios: *Cantidad*, *Precio unitario*

`PRODUCTO (1,N) — incluye — (0,N) PEDIDO`

- Un pedido incluye **de 1 a N** productos, porque no existen pedidos vacíos.
- Un producto puede aparecer en **de 0 a N** pedidos.
- La cantidad y el precio aplicado dependen del pedido y del producto. El precio unitario se guarda en la relación porque el precio del producto puede cambiar con el tiempo.

### 3.6 realiza (CLIENTE PLUS – PEDIDO) · **1:N**

`PEDIDO (0,N) — realiza — (1,1) CLIENTE PLUS`

- Un cliente *Tajinaste Plus* realiza **de 0 a N** pedidos desde su ingreso en el programa. Un cliente recién dado de alta aún no ha pedido nada.
- Cada pedido lo realiza **exactamente 1** cliente *Tajinaste Plus*.
- La relación se establece con el subtipo y no con CLIENTE, porque solo se controlan los pedidos de los clientes del programa.

### 3.7 Jerarquía CLIENTE → CLIENTE PLUS · **parcial**

`CLIENTE (1,1) — es un — (0,1) CLIENTE PLUS`

- **Parcial:** puede haber clientes que no pertenezcan al programa *Tajinaste Plus*.
- Cada cliente Plus es **exactamente 1** cliente (1,1), y un cliente es **como mucho 1** cliente Plus (0,1).
- Al haber un único subtipo, no se aplica la distinción entre jerarquía exclusiva y solapada.

### 3.8 tiene (CLIENTE PLUS – BONIFICACIÓN) · dependencia en identificación · **1:N**

`CLIENTE PLUS (1,1) — ID tiene — (0,N) BONIFICACIÓN`

- Un cliente Plus tiene **de 0 a N** bonificaciones, una por cada mes con compras.
- Cada bonificación pertenece a **exactamente 1** cliente Plus.
- BONIFICACIÓN es una entidad débil: el mes solo la distingue dentro de un mismo cliente.

---

## 4. Restricciones semánticas

Las siguientes reglas no pueden expresarse gráficamente en el modelo E/R y deberán garantizarse en fases posteriores, mediante restricciones `CHECK`, *triggers* o la lógica de la aplicación.

1. **Un único destino a la vez.** Los periodos `[Fecha inicio, Fecha fin]` de un mismo empleado en "trabaja en" **no pueden solaparse**. El enunciado indica que un empleado "nunca va a tener dos destinos" simultáneos. Como mucho puede haber un puesto con `Fecha fin` nula, que es el actual.
2. **Coherencia de fechas en los puestos.** `Fecha fin ≥ Fecha inicio` cuando `Fecha fin` no es nula, y `Fecha inicio ≥ Fecha contratación` del empleado.
3. **Stock no negativo.** `Stock ≥ 0`.
4. **Pedidos posteriores al ingreso.** La fecha de un pedido debe ser **igual o posterior** a la `Fecha ingreso` del cliente en el programa, porque solo se controlan los pedidos realizados desde su ingreso.
5. **Bonificaciones posteriores al ingreso.** El mes de una bonificación debe ser igual o posterior al mes de `Fecha ingreso` del cliente.
6. **Atributos derivados.**
   - `Importe total` de un pedido = Σ (`Cantidad` × `Precio unitario`) de sus productos.
   - `Volumen compras` de una bonificación = Σ `Importe total` de los pedidos del cliente cuya fecha cae en ese mes.
   - El `Importe` de la bonificación se calcula en función de ese volumen, según los tramos que defina la empresa.
7. **Georreferenciación válida.** Latitud ∈ [-90, 90] y longitud ∈ [-180, 180]. Además, las coordenadas de una zona deberían quedar próximas a las de su vivero.
8. **Valores positivos.** `Cantidad > 0`, `Precio ≥ 0`, `Precio unitario ≥ 0`, `Importe ≥ 0`.
9. **Gestión de pedidos.** El empleado responsable de un pedido debe estar dado de alta (contratado) en la fecha del pedido.
