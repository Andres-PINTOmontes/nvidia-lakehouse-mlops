# 🚀 NVIDIA Stock Price Prediction - Lakehouse MLOps Pipeline

[![Databricks](https://img.shields.io/badge/Databricks-Lakehouse-FF3621?logo=databricks)](https://databricks.com)
[![MLflow](https://img.shields.io/badge/MLflow-Tracking-0194E2?logo=mlflow)](https://mlflow.org)
[![Delta Lake](https://img.shields.io/badge/Delta_Lake-Storage-00ADD8)](https://delta.io)
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python)](https://python.org)

Proyecto completo de MLOps en Databricks para predecir movimientos de acciones de NVIDIA utilizando arquitectura Medallion (Bronze-Silver-Gold) y pipelines automatizados de ML.

---

## 📋 Tabla de Contenidos

- [Descripción General](#-descripción-general)
- [Arquitectura](#-arquitectura)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Flujo de Datos](#-flujo-de-datos)
- [Pipelines Automatizados](#-pipelines-automatizados)
- [Instalación](#-instalación)
- [Uso](#-uso)
- [Modelo de Machine Learning](#-modelo-de-machine-learning)
- [Dashboard](#-dashboard)
- [Tecnologías](#-tecnologías)
- [Mejoras Futuras](#-mejoras-futuras)

---

## 🎯 Descripción General

Este proyecto implementa un pipeline end-to-end de Machine Learning en Databricks para predecir si el precio de cierre de las acciones de NVIDIA subirá o bajará al día siguiente. Utiliza:

- **Arquitectura Medallion** (Bronze-Silver-Gold) para gestión de datos
- **Unity Catalog** para governance y linaje de datos
- **MLflow** para tracking de experimentos y registro de modelos
- **Delta Lake** para almacenamiento transaccional
- **Jobs programados** para ejecución automática (diaria/semanal)
- **Dashboard interactivo** con visualizaciones de análisis técnico

### 🎯 Objetivo del Negocio

Proporcionar predicciones diarias automatizadas del movimiento de acciones de NVIDIA para:
- Análisis técnico de tendencias
- Estrategias de trading algorítmico
- Monitoreo de volatilidad del mercado
- Evaluación continua del rendimiento del modelo

---

## 🏗️ Arquitectura

```
┌─────────────────────────────────────────────────────────────────┐
│                     NVIDIA STOCK MLOps PIPELINE                  │
└─────────────────────────────────────────────────────────────────┘

    📥 INGESTION          🧹 TRANSFORMATION        ⚡ FEATURES
    ─────────────         ─────────────────        ─────────────
    │                     │                        │
    │ Yahoo Finance       │ Data Cleaning          │ SMA 7/30 días
    │ API (yfinance)      │ Schema Validation      │ Volatilidad
    │                     │ Type Casting           │ Target Binary
    ▼                     ▼                        ▼
┌──────────┐         ┌──────────┐            ┌──────────┐
│ BRONZE   │────────▶│ SILVER   │───────────▶│  GOLD    │
│ Raw Data │         │ Curated  │            │ Features │
└──────────┘         └──────────┘            └──────────┘
                                                   │
    🤖 MACHINE LEARNING                            │
    ───────────────────                            │
                                                   │
    ┌──────────────────────────────────────────────┘
    │
    ▼
┌─────────────┐      ┌─────────────┐       ┌──────────────┐
│   TRAINING  │─────▶│   MLflow    │──────▶│ UC Registry  │
│ RandomForest│      │  Tracking   │       │ v1, v2, ...  │
└─────────────┘      └─────────────┘       └──────────────┘
                                                   │
    📊 DEPLOYMENT                                  │
    ─────────────                                  │
                                                   │
    ┌──────────────────────────────────────────────┘
    │
    ▼
┌─────────────┐      ┌─────────────┐       ┌──────────────┐
│   BATCH     │─────▶│ PREDICTIONS │──────▶│  DASHBOARD   │
│ INFERENCE   │      │   TABLE     │       │   AI/BI      │
└─────────────┘      └─────────────┘       └──────────────┘

    ⏰ ORCHESTRATION
    ────────────────
    
    📅 Daily Job (Mon-Fri 22:00 EST)
       ├─ Ingest → Clean → Features → Predict → Dashboard
    
    📅 Weekly Job (Sunday 04:00 EST)
       └─ Full Retrain + Model Registry Update
```

---

## 📁 Estructura del Proyecto

```
databricks_nvda/
│
├── 01_data_engineering/
│   ├── ingestion_bronze/
│   │   └── ingest_nvda_prices.ipynb       # 📥 Ingesta desde Yahoo Finance
│   └── transformation_silver/
│       └── clean_nvda_silver.ipynb        # 🧹 Limpieza y validación
│
├── 02_feature_engineering/
│   └── generate_stock_features.ipynb      # ⚡ Generación de features técnicas
│
├── 03_machine_learning/
│   ├── training/
│   │   └── train_stock_model.ipynb        # 🤖 Entrenamiento RandomForest
│   └── deployment/
│       └── batch_inference.ipynb          # 🔮 Predicciones batch diarias
│
├── 04_analytics/
│   └── dashboard_data_query.ipynb         # 📊 Preparación datos dashboard
│
├── 04_resources_&_config/
│   ├── environments/                       # 🔧 Configuraciones de entorno
│   └── workflows/                          # ⚙️ Definiciones de workflows
│
├── README.md                               # 📖 Este archivo
├── requirements.txt                        # 📦 Dependencias Python
└── .gitignore                              # 🚫 Archivos ignorados
```

---

## 🌊 Flujo de Datos

### 1️⃣ **Bronze Layer** (Raw Data)
- **Fuente**: Yahoo Finance API (yfinance)
- **Frecuencia**: 2 años de datos históricos
- **Intervalo**: Diario
- **Columnas**: Date, Open, High, Low, Close, Volume, ingestion_timestamp, source_name
- **Tabla**: `bronze_nvda_prices`

### 2️⃣ **Silver Layer** (Curated Data)
- **Transformaciones**:
  - Renombre de columnas a snake_case
  - Conversión de tipos (date, double, bigint)
  - Validación de esquema
  - Eliminación de duplicados
- **Tabla**: `silver_nvda_prices`

### 3️⃣ **Gold Layer** (Business-Level Data)
- **Features generadas**:
  - `sma_7`: Media móvil simple 7 días
  - `sma_30`: Media móvil simple 30 días
  - `daily_volatility`: Diferencia High-Low
  - `next_day_close`: Precio cierre día siguiente
  - `target`: 1 si sube, 0 si baja (clasificación binaria)
- **Tabla**: `gold_nvda_features`

### 4️⃣ **Dashboard Layer** (Enriched Analytics)
- **Columnas adicionales** (19 totales):
  - `Tendencia`: "Sube" / "Baja"
  - `daily_return`: Retorno % diario
  - `precio_sobre_sma7/30`: "Alcista" / "Bajista"
  - `volatilidad_categoria`: "Baja" / "Media" / "Alta"
  - `volumen_millones`: Volumen escalado
  - `distancia_sma7/30_pct`: Distancia % a medias móviles
- **Tabla**: `workspace.default.gold_nvda_dashboard`

---

## ⏰ Pipelines Automatizados

### 🔵 **NVDA_Daily_Inference_Pipeline**
**Objetivo**: Actualización diaria de predicciones
- **Schedule**: Lunes a Viernes, 22:00 EST (después del cierre del mercado)
- **Tasks** (5):
  1. `Ingesta_Bronze`: Descarga datos actualizados de Yahoo Finance
  2. `Limpieza_Silver`: Validación y transformación
  3. `Feature_Engineering`: Cálculo de indicadores técnicos
  4. `Batch_Inference`: Predicciones con modelo productivo
  5. `Dashboard_Refresh`: Actualización de datos enriquecidos

**Notificaciones**: Email on_success y on_failure

### 🟣 **NVDA_Weekly_Retraining_Pipeline**
**Objetivo**: Reentrenamiento semanal del modelo
- **Schedule**: Domingos, 04:00 EST
- **Tasks** (4):
  1. `Ingesta_Bronze`: Refresh completo de datos
  2. `Limpieza_Silver`: Validación
  3. `Feature_Engineering`: Regeneración de features
  4. `Model_Retraining`: Entrenamiento y registro en Unity Catalog

**Modelo**: RandomForestClassifier con 100 árboles
**Registry**: `workspace.default.nvda_price_classifier`

---

## 🚀 Instalación

### Requisitos Previos
- Cuenta de Databricks (AWS/Azure/GCP)
- Unity Catalog habilitado
- Serverless o cluster con DBR 14.3+

### 1. Clonar el Repositorio
```bash
git clone https://github.com/Andres-PINTOmontes/nvidia-lakehouse-mlops.git
cd nvidia-lakehouse-mlops
```

### 2. Importar en Databricks
**Opción A: Databricks CLI**
```bash
databricks workspace import_dir . /Users/<tu-email>/databricks_nvda --overwrite
```

**Opción B: UI de Databricks**
1. En Workspace, click en "Import"
2. Seleccionar "URL" y pegar: `https://github.com/Andres-PINTOmontes/nvidia-lakehouse-mlops`
3. O subir el `.zip` del proyecto

### 3. Configurar Unity Catalog
```sql
-- Crear catálogo y esquema si no existen
CREATE CATALOG IF NOT EXISTS workspace;
USE CATALOG workspace;
CREATE SCHEMA IF NOT EXISTS default;
```

### 4. Instalar Dependencias
Las dependencias se instalan automáticamente en los notebooks con:
```python
%pip install yfinance
```

---

## 💻 Uso

### Ejecución Manual (Orden Secuencial)

#### 1. Ingesta de Datos
```python
# Notebook: 01_data_engineering/ingestion_bronze/ingest_nvda_prices.ipynb
# Descarga 2 años de datos de Yahoo Finance y guarda en bronze_nvda_prices
```

#### 2. Limpieza y Transformación
```python
# Notebook: 01_data_engineering/transformation_silver/clean_nvda_silver.ipynb
# Transforma bronze → silver con validaciones
```

#### 3. Generación de Features
```python
# Notebook: 02_feature_engineering/generate_stock_features.ipynb
# Calcula SMA, volatilidad, target → gold_nvda_features
```

#### 4. Entrenamiento del Modelo
```python
# Notebook: 03_machine_learning/training/train_stock_model.ipynb
# Entrena RandomForest y registra en Unity Catalog
```

#### 5. Inferencia Batch
```python
# Notebook: 03_machine_learning/deployment/batch_inference.ipynb
# Genera predicciones y guarda en predictions_nvda
```

#### 6. Preparación Dashboard
```python
# Notebook: 04_analytics/dashboard_data_query.ipynb
# Enriquece datos → gold_nvda_dashboard
```

### Ejecución Automatizada (Jobs)

Los pipelines se ejecutan automáticamente según el schedule configurado. Para ejecutar manualmente:

```bash
# Daily Inference Pipeline
databricks jobs run-now --job-id <daily_job_id>

# Weekly Retraining Pipeline
databricks jobs run-now --job-id <weekly_job_id>
```

---

## 🤖 Modelo de Machine Learning

### Algoritmo: Random Forest Classifier

**Hiperparámetros**:
```python
{
    'n_estimators': 100,
    'max_depth': 10,
    'min_samples_split': 5,
    'random_state': 42
}
```

**Features de Entrada** (8):
- `open_price`, `high_price`, `low_price`, `close_price`
- `volume`
- `sma_7`, `sma_30`
- `daily_volatility`

**Target**:
- `1` → Precio subirá mañana
- `0` → Precio bajará mañana

**Métricas de Evaluación**:
- Accuracy: ~33% (baseline reportado)
- Precision, Recall, F1-Score
- Matriz de confusión

**MLflow Tracking**:
- Experimentos registrados automáticamente
- Artifacts: modelo, métricas, parámetros
- Model Registry: Unity Catalog (`workspace.default.nvda_price_classifier`)

---

## 📊 Dashboard

### Visualizaciones Implementadas

1. **Precio de Cierre vs SMAs (7 y 30 días)** [Line Chart Multi-Y]
   - Tendencias de corto y largo plazo
   - Cruces de medias móviles (señales de trading)

2. **Volumen Diario de Transacciones** [Bar Chart]
   - Actividad del mercado
   - Correlación con volatilidad

3. **Volatilidad del Mercado (%)** [Area Chart]
   - Riesgo diario
   - Períodos de alta/baja incertidumbre

4. **Días Alcistas vs Bajistas** [Pie Chart]
   - Distribución de tendencias históricas
   - Balance alcista/bajista

5. **Accuracy Histórica** [Counter]
   - Métrica de rendimiento del modelo
   - Formato: porcentaje

6. **Retorno Porcentual Diario (%)** [Line Chart con Anotación]
   - Ganancias/pérdidas diarias
   - Línea en y=0 como referencia

### Acceso al Dashboard
```
URL: https://dbc-647fdb67-c923.cloud.databricks.com/sql/dashboardsv3/<dashboard_id>
```

**Dataset**: `datasets/nvda_dashboard_data`  
**Tabla Fuente**: `workspace.default.gold_nvda_dashboard`

---

## 🛠️ Tecnologías

| Categoría | Tecnología | Uso |
|-----------|-----------|-----|
| **Platform** | Databricks | Plataforma Lakehouse unificada |
| **Storage** | Delta Lake | Almacenamiento transaccional ACID |
| **Catalog** | Unity Catalog | Governance y linaje de datos |
| **ML Tracking** | MLflow | Experimentos, modelos, artifacts |
| **Language** | Python 3.12 | Notebooks y scripts |
| **Compute** | Serverless | Ejecución escalable sin gestión |
| **Data Source** | Yahoo Finance (yfinance) | API de datos financieros |
| **ML Library** | scikit-learn | Random Forest Classifier |
| **Spark** | PySpark | Procesamiento distribuido |
| **Orchestration** | Databricks Jobs | Scheduling y workflows |
| **Visualization** | AI/BI Dashboards | Analytics interactivos |

---

## 🔮 Mejoras Futuras

### Machine Learning
- [ ] Experimentar con modelos avanzados (XGBoost, LightGBM, LSTM)
- [ ] Feature engineering adicional (RSI, MACD, Bollinger Bands)
- [ ] Optimización de hiperparámetros (Hyperopt)
- [ ] Ensemble de múltiples modelos
- [ ] Predicciones multi-step (3, 5, 7 días)

### MLOps
- [ ] A/B Testing con modelos campeón/retador
- [ ] Monitoring de drift de datos y modelo
- [ ] Alertas automáticas por degradación de accuracy
- [ ] CI/CD con Databricks Asset Bundles (DABs)
- [ ] Tests automatizados de calidad de datos

### Dashboard
- [ ] Configuración manual de encodings de widgets
- [ ] Publicación de dashboard para acceso externo
- [ ] Filtros interactivos por fecha y métricas
- [ ] Integración con Databricks SQL Alerts

### Data Pipeline
- [ ] Ingesta en tiempo real (Structured Streaming)
- [ ] Integración con múltiples fuentes (Bloomberg, Alpha Vantage)
- [ ] Procesamiento incremental con Change Data Feed
- [ ] Particionamiento por año/mes para optimización

---

## 📝 Licencia

Este proyecto es de código abierto bajo la licencia MIT.

---

## 👤 Autor

**Andrés Pinto Montes**

- GitHub: [@Andres-PINTOmontes](https://github.com/Andres-PINTOmontes)
- LinkedIn: [Andrés Pinto Montes](https://linkedin.com/in/andres-pinto-montes)
- Email: 4c9m0n7e5@gmail.com

---

## 🙏 Agradecimientos

- **Databricks** por la plataforma Lakehouse
- **Yahoo Finance** por la API de datos financieros
- **MLflow** por el framework de ML tracking
- **Comunidad de Data & AI** por el apoyo continuo

---

## 📚 Referencias

- [Databricks Documentation](https://docs.databricks.com/)
- [MLflow Documentation](https://mlflow.org/docs/latest/index.html)
- [Delta Lake Documentation](https://docs.delta.io/)
- [Unity Catalog Best Practices](https://docs.databricks.com/data-governance/unity-catalog/index.html)
- [yfinance Documentation](https://pypi.org/project/yfinance/)

---

⭐ **¡Dale una estrella si este proyecto te fue útil!** ⭐
