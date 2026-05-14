# Diccionario de Datos - ventas_clean.csv

Este documento describe cada una de las columnas presentes en el dataset limpio `ventas_clean.csv`, resultado del proceso ETL documentado en el README.

---

## 📅 Columnas temporales

| Columna | Tipo | Descripción | Rango / Ejemplo |
|---------|------|-------------|-----------------|
| `FechaVenta` | Date | Fecha en que se realizó la transacción | `2015-12-31` |
| `Año` | Whole Number | Año extraído de `FechaVenta` | `2015`, `2019`, `2022` |
| `Mes` | Whole Number | Mes extraído de `FechaVenta` (1 al 12) | `1` (Enero), `12` (Diciembre) |
| `AñoMes` | Whole Number | Clave numérica para orden cronológico | `201512`, `201903`, `202210` |
| `HoraVenta` | Time | Hora en que se realizó la transacción | `05:42:43`, `19:03:21` |
| `FranjaHoraria` | Text | Segmento horario clasificado | `Madrugada (0-6)`, `Mañana (6-12)`, `Tarde (12-18)`, `Noche (18-22)`, `Noche avanzada (22-24)` |

---

## 🏪 Columnas de sucursal

| Columna | Tipo | Descripción | Ejemplo |
|---------|------|-------------|---------|
| `Sucursal_Nombre` | Text | Nombre normalizado de la sucursal | `TechCore Medellín #1`, `TechCore Pereira`, `TechCore Cali` |
| `Ciudad_Sucursal` | Text | Ciudad donde se ubica la sucursal (imputada desde `Sucursal_Nombre`) | `Medellín`, `Pereira`, `Cali`, `Bogotá` |

---

## 👤 Columnas de vendedor y cliente

| Columna | Tipo | Descripción | Ejemplo |
|---------|------|-------------|---------|
| `Vendedor_Nombre` | Text | Nombre del vendedor que atendió la venta | `Amílcar Ortega-Alberto` |
| `Cliente_Nombre` | Text | Nombre completo del cliente | `Bienvenida Nebot-Fiol` |
| `Genero_Cliente` | Text | Género del cliente | `F` (Femenino), `M` (Masculino) |
| `Edad_cliente` | Whole Number | Edad del cliente en años | `22`, `37`, `51` |
| `RangoEdad` | Text | Segmento etario derivado de `Edad_cliente` | `18-24`, `25-34`, `35-49`, `50+` |
| `Email_Cliente` | Text | Correo electrónico del cliente | `bienvenida19@hotmail.com` |
| `Telefono_Cliente` | Text | Número de teléfono de contacto | `+34806548767` |
| `Direccion_Cliente` | Text | Dirección de facturación | `cll 52 #32-98` |

---

## 💳 Método de pago

| Columna | Tipo | Descripción | Ejemplo |
|---------|------|-------------|---------|
| `Metodo_Pago` | Text | Método de pago utilizado en la transacción | `Tarjeta Crédito`, `Billetera Digital`, `Tarjeta Débito` |

---

## 🖥️ Columnas de productos (estructura repetida para Producto1, Producto2, Producto3)

### Para cada producto (1, 2 y 3):

| Columna | Tipo | Descripción | Ejemplo |
|---------|------|-------------|---------|
| `Nombre_ProductoN` | Text | Nombre del producto | `Apple MacBook Pro 16` |
| `Marca_ProductoN` | Text | Marca normalizada del producto | `Apple`, `Dell`, `Lenovo`, `HP` |
| `Cantidad_ProductoN` | Whole Number | Cantidad de unidades vendidas del producto | `1`, `2` |
| `Precio_Unitario_ProductoN` | Decimal | Precio unitario del producto | `9600000.00` |
| `Subtotal_ProductoN` | Decimal | Subtotal = Cantidad × Precio Unitario | `9600000.00` |

> **Nota:** Para `Nombre_Producto2` y `Nombre_Producto3`, cuando el cliente no llevó ese producto, el valor es `"No aplica"` y su `Subtotal_ProductoN` es `0`.

---

## 💰 Columnas de totales y descuentos

| Columna | Tipo | Descripción | Ejemplo |
|---------|------|-------------|---------|
| `Descuento_Venta` | Decimal | Porcentaje de descuento aplicado a toda la venta | `0.0`, `0.05`, `0.15` |
| `TieneDescuento` | Boolean | Indica si la venta tuvo algún descuento (`true` = sí, `false` = no) | `true`, `false` |
| `Total_Venta` | Decimal | Total final de la factura después de aplicar descuento | `31200000.00` |
| `TotalProductos` | Whole Number | Suma de `Cantidad_Producto1` + `Cantidad_Producto2` + `Cantidad_Producto3` | `1`, `3`, `5` |

---

## 📊 Resumen de tipos por categoría

| Categoría | Tipos de datos |
|-----------|----------------|
| Fechas y horas | `Date`, `Time`, `Whole Number`, `Text` |
| Texto categórico | `Text` |
| Numéricas (cantidades) | `Whole Number` |
| Numéricas (precios, subtotales, totales) | `Decimal Number` |
| Booleanas | `Boolean` |

---

## 🔍 Notas importantes

1. **Valores nulos imputados:**
   - Columnas categóricas (texto) → `"No especificado"`
   - Columnas numéricas → `0`
   - `Nombre_Producto2` / `Nombre_Producto3` con subtotal = 0 → `"No aplica"`

2. **Normalización aplicada:**
   - Sucursales: unificadas con mayúsculas consistentes y tildes correctas
   - Ciudades: extraídas desde `Sucursal_Nombre` cuando faltaban
   - Marcas: corregidas tipográficamente (ej: `Aple` → `Apple`)

3. **Columnas eliminadas:**
   - `VentaID`: sin valor analítico (desordenada y no secuencial)

---

## 📁 Última actualización

**Fecha:** Mayo 2026  
**Versión del dataset:** 1.0  
**Origen:** `ventas_clean.csv` exportado desde Power BI Desktop
