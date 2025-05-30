


# 1. investigacion reto:

 #### **¿Qué es una base de datos NoSQL?**

bases de datos no relacionales que almacenan datos en un formato no tabular, en lugar de hacerlo en tablas relacionales basadas en reglas

#### **¿Qué es MongoDB?**

es un sistema de gestión de bases de datos (DBMS) no relacional de código abierto que utiliza documentos flexibles en lugar de tablas y filas para procesar y almacenar diversas formas de datos

#### **¿Qué diferencia hay entre una base de datos relacional (como MySQL) y una base de datos documental como MongoDB?**

| Característica               | MySQL (Relacional)                               | MongoDB (Documental)                              |
|------------------------------|--------------------------------------------------|---------------------------------------------------|
| **Estructura de datos**      | Tablas con filas y columnas                      | Documentos en colecciones                         |
| **Esquema fijo**             | Sí (definido con antelación)                     | No (esquema flexible por documento)               |
| **Formato de los datos**     | Registros estructurados en SQL                   | Documentos en JSON / BSON                         |
| **Relaciones entre datos**   | Claves primarias y foráneas                      | Información anidada en un solo documento          |
| **Consultas**                | Lenguaje SQL                                     | Consultas tipo JSON                               |
| **Ideal para**               | Datos estructurados, relaciones complejas        | Datos variados y flexibles, sin esquema fijo      |
---

#### **¿Qué son documentos y colecciones en MongoDB?**

Un **documento** en MongoDB es un objeto en formato JSON que representa una unidad de información. Es equivalente a una "fila" en una tabla SQL, pero mucho más flexible.

```json
[
  {
    "nombre": "panzarottis",
    "ingredientes": ["queso", "tomate"],
    "precio": 15.000,
    "disponible": true
  },
  {
    "nombre": "Pizza",
    "ingredientes": ["queso", "tomate", "pepperoni"],
    "precio": 9.0,
    "disponible": false
  }
]
```

# 2. Diseñar nuestra propuesta

Para este modelo documental con MongoDB, pensamos en cómo organizar la información de la pizzería utilizando **colecciones** y **documentos JSON** .

###  Colecciones propuestas:

- `productos`: Almacena todos los productos individuales que se venden (pizzas, panzarottis, bebidas, postres, adiciones).
- `combos`: Conjuntos de productos ofrecidos como promociones o paquetes especiales.
- `clientes`: Información básica de los clientes que hacen pedidos.
- `pedidos`: Registra los pedidos hechos por los clientes.
- `ingredientes`: Lista de ingredientes utilizados en la preparación de productos (opcional si se incrustan directamente en los productos).

---

###  Estructura de documentos

####  Producto (ej. pizza)
```json
/* PRODUCTO */ 
{
    "_id": "prod001",
  "nombre": "Pizza Hawaiana",
  "tipo": "pizza",
  "precio": 25000,
  "ingredientes": ["piña", "jamón", "queso mozzarella"]
}

/* COMBO */
{
  "_id": "combo01",
  "nombre": "Combo Familiar",
  "precio": 45000,
  "productos": [
    { "producto_id": "prod001", "nombre": "Pizza Hawaiana", "cantidad": 1 },
    { "producto_id": "prod004", "nombre": "Gaseosa 1.5L", "cantidad": 2 }
  ]
}

/* PEDIDO */ 
{
  "_id": "pedido123",
  "cliente": {
    "nombre": "Laura Ruiz",
    "telefono": "3001234567"
  },
  "fecha": "2025-05-29T19:00:00Z",
  "modo": "para recoger",
  "productos": [
    {
      "producto_id": "prod001",
      "nombre": "Pizza Hawaiana",
      "adiciones": ["queso extra", "tocineta"],
      "cantidad": 1
    },
    {
      "producto_id": "prod004",
      "nombre": "Gaseosa 400ml",
      "cantidad": 2
    }
  ],
  "total": 32000
}
```

### ¿Qué se incrusta y qué se referencia?

Los productos y datos del cliente se incrustan dentro del documento del pedido. Esto nos permite conservar exactamente cómo fue el pedido, incluso si después cambian los precios o ingredientes.

Las colecciones están desnormalizadas: preferimos guardar los datos completos del producto dentro del pedido, en lugar de solo el ID, para facilitar la lectura del historial y reducir la necesidad de múltiples consultas.

---

###  Tipos de datos usados:

- **Listas (arrays)**: para ingredientes, productos en combos o pedidos, y adiciones.
- **Objetos**: para representar al cliente o los productos dentro del pedido.
- **Strings, números, fechas**: para los campos comunes como nombres, precios, teléfonos, fecha del pedido, etc.

# 3. Ejemplos de documentos JSON

A continuación presentamos tres documentos de ejemplo creados para representar:

- Un producto (pizza)
- Un combo
- Un pedido con cliente y productos personalizados

---

### Producto (ej. pizza con ingredientes)
- Este documento representa un producto individual de tipo pizza.
- Incluye una lista de ingredientes y su precio.
```json
{
  "_id": "prod001",
  "nombre": "Pizza Mexicana",
  "tipo": "pizza",
  "precio": 27000,
  "ingredientes": ["jalapeños", "chorizo", "queso mozzarella", "tomate", "cebolla morada"]
}
```
### Combo
- Aquí mostramos un combo que agrupa varios productos.
- Cada producto dentro del combo incluye su ID, nombre y cantidad.
```json
{
  "_id": "combo01",
  "nombre": "Combo Pareja",
  "precio": 40000,
  "productos": [
    {
      "producto_id": "prod001",
      "nombre": "Pizza Mexicana",
      "cantidad": 1
    },
    {
      "producto_id": "prod005",
      "nombre": "Gaseosa 1.5L",
      "cantidad": 1
    },
    {
      "producto_id": "prod006",
      "nombre": "Brownie con helado",
      "cantidad": 1
    }
  ]
}
```
###  Pedido con cliente y productos personalizados
- Este pedido incluye datos del cliente incrustados y productos personalizados con adiciones.
- También se incluye el modo del pedido (en el lugar) y el total calculado.
```json
{
  "_id": "pedido789",
  "cliente": {
    "nombre": "Carlos Mendoza",
    "telefono": "3114567890"
  },
  "fecha": "2025-05-29T20:45:00Z",
  "modo": "en el lugar",
  "productos": [
    {
      "producto_id": "prod001",
      "nombre": "Pizza Mexicana",
      "cantidad": 1,
      "adiciones": ["queso extra", "tocineta"]
    },
    {
      "producto_id": "prod006",
      "nombre": "Brownie con helado",
      "cantidad": 2
    }
  ],
  "total": 51000
}
```

# 4. Reflexión grupal

### ¿Qué fue lo más difícil de imaginar sin tablas?

Lo más difícil fue dejar de pensar en tablas y relaciones como en MySQL. Al principio queríamos separar todo en colecciones distintas y usar solo referencias, pero nos dimos cuenta de que en MongoDB muchas veces es mejor incrustar la información directamente, aunque repitamos datos, para que sea más fácil consultar después.

### ¿Qué nos gustó del enfoque con documentos?

Nos gustó la flexibilidad que ofrece MongoDB. Poder tener campos dinámicos, listas, objetos dentro de objetos, e incluso adiciones personalizadas en cada producto del pedido, nos pareció muy útil. Sentimos que se parece mucho a cómo pensamos los datos en la vida real.

###  ¿Qué dudas nos surgieron?

Nos preguntamos cuándo es mejor incrustar la información y cuándo referenciar. También nos generó dudas cómo actualizar los datos si hay cambios (por ejemplo, si sube el precio de una pizza) y cómo evitar inconsistencias cuando los datos se repiten en varios documentos.

---