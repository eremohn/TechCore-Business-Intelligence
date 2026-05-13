# TechCore - Inteligencia de Negocio para Ventas Tecnológicas

## 📌 Introducción

TechCore es una cadena minorista especializada en la venta de computadores y accesorios tecnológicos. Este proyecto forma parte del proceso de análisis de datos de la compañía y tiene como objetivo principal **transformar una base de datos cruda de facturación en un conjunto de datos limpio, estructurado y listo para su uso en Power BI**.

La información original proviene de un archivo CSV con registros de ventas que presentaba múltiples problemas de calidad: duplicados, valores nulos, texto no normalizado, errores tipográficos e inconsistencias en las categorías. Para solucionarlo, se implementó un proceso completo de **ETL (Extracción, Transformación y Carga)** utilizando el editor **Power Query** de Power BI.

Este README documenta cada paso realizado, las decisiones tomadas y los criterios aplicados durante la limpieza y estandarización de los datos, con el fin de garantizar la reproducibilidad del proceso y servir como base para futuros análisis y modelados.

---

## 🎯 Objetivos del Proyecto

1. **Importar** el archivo CSV original de facturación a Power BI Desktop.
2. **Limpiar y estandarizar** los datos mediante Power Query, aplicando transformaciones que aseguren calidad, coherencia y usabilidad.
3. **Normalizar** los campos categóricos (sucursales, ciudades, marcas, nombres de productos) para eliminar variaciones que dificultan el análisis.
4. **Tratar valores nulos y duplicados** según su naturaleza, imputando valores por defecto (como `"No especificado"`, `"No aplica"` o `0`) cuando corresponda.
5. **Definir tipos de datos correctos** para cada columna: fechas como `Date`, precios como `Decimal Number`, identificadores como `Text`, etc.
6. **Dejar el conjunto de datos listo** para las siguientes etapas del proyecto: análisis exploratorio, modelado semántico y creación de dashboards en Power BI.

---

## 🛠️ Proceso de Limpieza y Transformación

A continuación se detalla paso a paso el proceso de limpieza aplicado en Power Query.

### 0. Configuración Regional (previo a la importación)

Antes de importar el archivo CSV, se configuró Power BI Desktop con la regionalización **Inglés (Estados Unidos)** para interpretar correctamente:
- Los separadores decimales con **punto (.)** en lugar de coma (,)
- Las fechas en formato compatible con el origen de datos

**Ruta:** `Archivo` → `Opciones y configuración` → `Opciones` → `Configuración regional` → `Inglés (Estados Unidos)`

Esta configuración evita errores de multiplicación por 10 en valores numéricos y problemas de interpretación de fechas.


### 1. Carga de datos

- **Origen:** archivo `ventas.csv`
- **Formato:** CSV codificado en UTF-8
- **Herramienta:** Power BI Desktop → Editor de Power Query

### 2. Eliminación de duplicados

Se eliminaron registros de facturas **exactamente iguales en todas sus columnas**.

- **Método:** selección de todas las columnas → `Inicio` → `Quitar filas` → `Quitar duplicados`
- **Justificación:** evitar que una misma transacción aparezca múltiples veces en los resúmenes y cálculos.

### 3. Manejo de valores nulos y vacíos

| Tipo de columna | Situación | Valor imputado |
|----------------|-----------|----------------|
| Categórica (texto) | Celda vacía o null | `"No especificado"` |
| Numérica | null | `0` |
| NombreProducto2 / NombreProducto3 | Vacío con subtotal = 0 | `"No aplica"` |

> **Nota:** En las columnas `NombreProducto2` y `NombreProducto3` se utilizó `"No aplica"` para distinguir cuando el cliente **no llevó ese producto** (subtotal = 0) de un caso donde sí llevó un producto pero faltó el nombre (subtotal > 0 con nombre vacío).

### 4. Normalización de columnas categóricas

#### 4.1 SucursalNombre

Se unificaron variaciones de escritura (mayúsculas, minúsculas, tildes, espacios, ausencia de `#`) bajo un nombre estándar.

| Original detectado | Estandarizado |
|--------------------|----------------|
| `TechCore Medellin #1`, `TechCore medellín #1`, `TechCore medellín#1`, `Techcore Medellín #1` | `TechCore Medellín #1` |
| `TechCore Medellin #2`, `TechCore medellín #2`, `TechCore medellín#2` | `TechCore Medellín #2` |
| `TechCore cali` | `TechCore Cali` |
| `TechCore pereira`, `Techcore Pereíra`, `TechCore pereíra` | `TechCore Pereira` |
| `Techcore bogotá #2`, `TechCore bogota#2`, `Techcore bogotá#2` | `TechCore Bogotá #2` |
| `Techcore bogotá #1`, `TechCore bogota#1`, `Techcore bogotá#1` | `TechCore Bogotá #1` |

#### 4.2 CiudadSucursal

| Original detectado | Estandarizado |
|--------------------|----------------|
| `bogota`, `Bogotá`, `Bogotá` (duplicado) | `Bogotá` |
| `cali` | `Cali` |
| `Medellín`, `medellin`, `Medellín` | `Medellín` |
| `Pereíra`, `Pereira`, `pereíra` | `Pereira` |

**Corrección de valores faltantes:**  
Los valores nulos o "No especificado" en `CiudadSucursal` se imputaron automáticamente extrayendo la ciudad desde `SucursalNombre` mediante la función `Text.BetweenDelimiters`. Esto garantiza que toda factura tenga una ciudad válida y consistente con la sucursal registrada.


#### 4.3 Marcas de productos

Se corrigieron errores tipográficos en las columnas `MarcaProducto1`, `MarcaProducto2`, `MarcaProducto3`.

| Original | Corregido |
|----------|-----------|
| `Acr` | `Acer` |
| `Aple` | `Apple` |
| `Azus` | `Asus` |
| `Del` | `Dell` |
| `Delll` | `Dell` |
| `HPP` | `HP` |
| `Leno` | `Lenovo` |
| `Lenovovo` | `Lenovo` |
| `Lenvo` | `Lenovo` |
| `Microsofft` | `Microsoft` |
| `MSY` | `MSI` |
| `Samsng` | `Samsung` |
| `Razón` | `Razer` |

### 5. Tipos de datos aplicados

| Tipo de columna | Formato asignado |
|----------------|------------------|
| Fechas (`FechaVenta`) | `Date` |
| Horas (`HoraVenta`) | `Time` |
| Precios, subtotales, totales | `Decimal Number` |
| Cantidades | `Whole Number` |
| IDs, nombres, textos categóricos | `Text` |

---
### 6. Creación de columnas derivadas para análisis

Con el objetivo de enriquecer el análisis y facilitar segmentaciones futuras, se crearon las siguientes columnas a partir de las existentes:

#### Columnas temporales (desde `FechaVenta`)

| Columna creada | Fórmula en Power Query | Descripción |
|----------------|------------------------|-------------|
| `Año` | `Date.Year([FechaVenta])` | Extrae el año de la venta |
| `Mes` | `Date.Month([FechaVenta])` | Extrae el número del mes (1 al 12) |
| `AñoMes` | `[Año] * 100 + [Mes]` | Clave numérica para orden cronológico (ej: 202512) |

#### Segmentación de clientes (desde `edadcliente`)

| Columna creada | Fórmula | Descripción |
|----------------|---------|-------------|
| `RangoEdad` | `if [Edad_cliente] < 18 then "Menor de 18" else if [Edad_cliente] < 25 then "18-24" else if [Edad_cliente] < 35 then "25-34" else if [Edad_cliente] < 50 then "35-49" else "50+"` | Agrupa clientes por rangos etarios para análisis demográfico |

#### Comportamiento de compra (desde `HoraVenta`)

| Columna creada | Fórmula | Descripción |
|----------------|---------|-------------|
| `FranjaHoraria` | `let hora = Time.Hour([HoraVenta]) in if hora < 6 then "Madrugada (0-6)" else if hora < 12 then "Mañana (6-12)" else if hora < 18 then "Tarde (12-18)" else if hora < 22 then "Noche (18-22)" else "Noche avanzada (22-24)"` | Clasifica la venta según la hora del día para identificar hábitos de consumo |

#### Métricas de la factura

| Columna creada | Fórmula | Descripción |
|----------------|---------|-------------|
| `TotalProductos` | `[Cantidad_Producto1] + [Cantidad_Producto2] + [Cantidad_Producto3]` | Número total de unidades vendidas en la factura |
| `TieneDescuento` | `[Descuento_Venta] > 0` | Booleano que indica si la venta tuvo algún descuento aplicado |

#### Ejemplo visual del resultado

| FechaVenta | HoraVenta | edadcliente | TotalVenta | Año | Mes | RangoEdad | FranjaHoraria | TotalProductos | TieneDescuento |
|------------|-----------|-------------|------------|-----|-----|-----------|---------------|----------------|----------------|
| 2022-10-02 | 11:56:06 | 36 | 12.920.000 | 2022 | 10 | 35-49 | Mañana (6-12) | 3 | true |
| 2022-06-04 | 07:00:48 | 43 | 23.940.000 | 2022 | 6 | 35-49 | Mañana (6-12) | 5 | true |
| 2019-01-14 | 12:27:53 | 35 | 4.800.000 | 2019 | 1 | 35-49 | Tarde (12-18) | 1 | false |

> **Nota:** La columna `TotalProductos` suma las cantidades de los tres productos posibles por factura. El valor máximo posible es la suma de `Cantidad_Producto1`, `Cantidad_Producto2` y `Cantidad_Producto3`.

---

## 📁 Estructura del Proyecto



```bash
TechCore-Business-Intelligence/
│
├── data/
│   ├── raw/
│   │   └── ventas.csv                 # Original (crudo, nunca se modifica)
│   │
│   └── processed/
│       └── ventas_clean.csv           # Exportado desde Power BI (datos listos)
│
├── powerbi/
│   └── TechCore_Cleaning.pbix         # Archivo con todo el ETL aplicado
│
├── docs/
│   └── columnas_diccionario.md        # Diccionario de datos
│
├── README.md                          # Documentación de la fase ETL
│
└── CHANGELOG.md                       # Registro de cambios


```


---

## 🚀 Cómo Reproducir este Proceso

1. Clona este repositorio:
   ```bash
   git clone https://github.com/eremohn/TechCore-Business-Intelligence.git
2. Abre **Power BI Desktop**.
3. Abre el archivo powerbi/TechCore_Cleaning.pbix.


---

## 🔮 Próximos Pasos (Futuras Etapas)

- [ ] Crear un **modelo semántico** en Power BI con relaciones entre tablas (fechas, productos, sucursales, clientes).
- [ ] Diseñar **medidas DAX** para KPIs clave: ingresos totales, ticket promedio, productos más vendidos, etc.
- [ ] Construir **dashboards interactivos** con filtros por sucursal, ciudad, método de pago, rango de fechas.
- [ ] Implementar **validaciones de calidad** adicionales (rangos, integridad referencial).
- [ ] Automatizar la actualización de datos desde el origen (CSV, base de datos, API).

---

## 📄 Licencia

Este proyecto es de uso interno para fines de aprendizaje y demostración del proceso de limpieza de datos con Power Query.

---

## 📬 Contacto

Para dudas, sugerencias o reportar inconsistencias en la normalización, abrir un **issue** en el repositorio o contactar al mantenedor del proyecto.
