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

## 📁 Estructura del Proyecto



```bash
TechCore-Sales-Analytics/
│
├── data/
│ └── ventas.csv # Archivo original de facturación
│
├── powerbi/
│ └── TechCore_Cleaning.pbix # Archivo de Power BI con el proceso ETL aplicado
│
├── README.md # Este archivo
│
└── CHANGELOG.md # Registro de cambios futuros (opcional)


```


---

## 🚀 Cómo Reproducir este Proceso

1. Clona este repositorio o descarga el archivo `.pbix`.
2. Abre **Power BI Desktop**.
3. Carga el archivo `ventas.csv` desde la carpeta `data/`.
4. Abre el **Editor de Power Query** (`Inicio` → `Transformar datos`).
5. Revisa los **pasos aplicados** en cada consulta. Cada transformación documentada en este README está implementada paso a paso.
6. Refresca la vista y verifica los datos limpios.

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
