# Análisis y Predicción de la Extorsión en Colombia

> **Nota sobre reproducibilidad**: Este proyecto consume datos en tiempo real desde APIs oficiales del Estado colombiano (datos.gov.co, DANE, UNODC). Los resultados numéricos (métricas del modelo, estadísticas descriptivas) pueden variar levemente entre ejecuciones a medida que las fuentes actualizan sus registros.

---

## Estructura del Proyecto

```
Delitos_Colombia/
├── 01_procesamiento.py       ← ETL: Bronze → Silver → Gold
├── 02_eda.py                 ← Análisis exploratorio (ilustraciones 1–20)
├── 03_modelacion.py          ← Modelado predictivo (ilustraciones 21–26)
│
├── datos/
│   ├── silver/               ← 10 datasets individuales limpios
│   └── gold/
│       ├── consolidado_analitico.csv   (dataset completo 2015–2025)
│       └── consolidado_modelado.csv    (dataset de modelado 2020–2025, 16.869 filas)
│
├── outputs/
│   ├── eda/graficos/         ← Ilustraciones 1–20 (PNG, 300 DPI)
│   └── modelacion/
│       ├── figuras/          ← Ilustraciones 21–26 (PNG, 300 DPI)
│       └── metricas/         ← comparacion_modelos.csv
│
├── docs/
│   ├── plantilla_individual_ajustada_2.pdf   ← Documento final del TFM
│
├── bases_de_origen/          ← Archivos fuente originales
└── _archivo/                 ← Versiones anteriores archivadas
```

---

## Pipeline de Ejecución

Ejecutar los scripts en orden desde la raíz del proyecto:

```bash
# Paso 1: Procesar datos (requiere conexión a internet ~20 min)
python 01_procesamiento.py

# Paso 2: Análisis exploratorio (~5 min)
python 02_eda.py

# Paso 3: Modelación predictiva (~5 min)
python 03_modelacion.py
```

> **Nota**: Si los datos Silver/Gold ya existen en `datos/`, se puede saltar el paso 1
> y ejecutar directamente 02 y 03.

---

## Metodología — CRISP-DM

### 1. Comprensión del Negocio
Predecir la incidencia de extorsión a nivel municipal-mensual en Colombia (2020–2025)
usando indicadores socioeconómicos y de criminalidad correlacionados.

### 2. Datos — Arquitectura Medallion

| Capa   | Descripción | Ruta |
|--------|-------------|------|
| Bronze | Datos crudos desde APIs (Socrata) y archivos DANE | (descarga en vivo) |
| Silver | Datasets individuales limpios y estandarizados | `datos/silver/` |
| Gold   | Dataset consolidado para modelado | `datos/gold/` |

**Fuentes de datos**:
- Policía Nacional: Extorsión, Homicidio, Hurto, Terrorismo, Estupefacientes
- DANE: Proyecciones de Población 2020–2035
- UNODC/SIMCI: Cultivos de Coca por municipio
- MinTIC: Cobertura móvil por municipio
- DANE: PIB departamental

### 3. Análisis Exploratorio (EDA)
Ver `02_eda.py` y `outputs/eda/graficos/`.

### 4. Modelación
**Dataset de modelado**: `datos/gold/consolidado_modelado.csv`
- 16.869 registros municipio-mes (2020–2025)
- Solo municipios con al menos 1 caso de extorsión registrado
- División temporal: Train 2020–2023 / Test 2024–2025

---

## Resultados del Modelo

> Las métricas a continuación corresponden al corte de datos de **noviembre 2025** (versión del documento final). Al ejecutar el pipeline con datos actualizados los resultados pueden variar levemente dado que las APIs siguen incorporando nuevos registros.

| Modelo | MAE Test | RMSE Test | R² Test | R² Train | Overfitting |
|--------|----------|-----------|---------|----------|-------------|
| **Gradient Boosting** | **1.68** | **6.51** | **0.8046** | 0.9377 | 13.3% |
| Random Forest | 1.59 | 6.73 | 0.7910 | 0.9777 | 18.7% |
| XGBoost | 1.74 | 7.09 | 0.7681 | 0.9082 | 14.0% |
| Regresión Lineal | 2.11 | 7.13 | 0.7653 | 0.7902 | 2.5% |

**Mejor modelo**: Gradient Boosting — R² Test = 0.8046 (mejor balance precisión/sobreajuste)

**Variables más influyentes** (Gradient Boosting):
1. Población Total (~60%)
2. Total Hurto (~25%)
3. Incautaciones de Estupefacientes (~4%)

---

## Stack Tecnológico

```
Python 3.x · pandas · numpy · scikit-learn · xgboost
matplotlib · seaborn · scipy · requests · openpyxl
```

Instalar dependencias:
```bash
pip install pandas numpy scikit-learn xgboost matplotlib seaborn scipy requests openpyxl
```
