🚦 Clasificación Espaciotemporal de Siniestralidad Vial en Madrid mediante XGBoost (2023–2025)
![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python)
![XGBoost](https://img.shields.io/badge/XGBoost-Gradient%20Boosting-green)
![Metodología](https://img.shields.io/badge/Metodolog%C3%ADa-CRISP--DM-orange)
Repositorio oficial de código fuente, pipelines de ingeniería espaciotemporal y artefactos de reproducibilidad del Trabajo de Fin de Máster (TFM): "Desarrollo y evaluación comparativa de un modelo XGBoost para la clasificación espaciotemporal de accidentes en Madrid".
---
📋 Resumen del Estudio
El proyecto aplica la metodología CRISP-DM sobre los registros oficiales abiertos de accidentalidad del Ayuntamiento de Madrid (2023–2025), discretizando la ciudad en una malla regular UTM de $100 \times 100\text{ m}$ y aplicando una partición temporal estricta sin fuga de información (data leakage):
Entrenamiento (`2023–2024`): Modelado sobre unidades espaciotemporales balanceadas en proporción controlada $1:3$ (1 positivo por cada 3 negativos).
Evaluación fuera de muestra (`2025`): Contraste de 5 procedimientos predictivos sobre 85.804 unidades independientes ($21.451$ positivas y $64.353$ negativas).
---
📊 Resultados Consolidados Fuera de Muestra (Test 2025)
Procedimiento	Precisión Media (AP)	ROC-AUC	F1-Score	Precisión	Exhaustividad (Recall)
🏆 XGBoost Híbrido Out-of-Fold (OOF)	0.5362	0.7621	0.5258	0.4002	0.7660
🌲 XGBoost Híbrido Estándar	0.5259	0.7345	0.5060	0.4287	0.6175
📊 Baseline Histórico Espacial	0.4595	0.6554	0.4283	0.2975	0.7643
🟦 XGBoost Base (5 variables)	0.4256	0.7019	0.4761	0.3590	0.7068
📈 Regresión Logística Múltiple	0.2923	0.6023	0.4122	0.3115	0.6091
Hallazgos Principales
Robustez frente al sesgo espacial: La tasa de retención del Average Precision entre el enfoque Híbrido Out-of-Fold ($2023 \leftrightarrow 2024$) y el Híbrido Estándar alcanza el 101,96 %, descartando look-ahead leakage o sobreajuste espacial.
Priorización territorial (Malla $100 \times 100\text{ m}$): Mediante el umbral del Percentil 95 calculado estrictamente en entrenamiento ($\ge 12,7584$), apenas 82 celdas (0,59 % de las $13.828$ evaluadas en 2025) concentran el 7,59 % de los siniestros reales (factor de focalización de 12,8x).
Explicabilidad (XAI Global): En el modelo híbrido Out-of-Fold, la importancia relativa nativa se distribuye en `pos_train_hist` (62,3 %), `hora_int` (18,8 %), `tipo_dia` (9,8 %), `grid_x` (3,4 %), `grid_y` (3,1 %) y `mes` (2,5 %).
---
🗂️ Estructura del Código (`anexo_codigo_completo_modelo.py`)
El pipeline completo está organizado en 7 bloques secuenciales reproducibles con semilla fija (`RANDOM_STATE = 42`):
Fase 0 — Configuración y Carga: Descarga directa y consolidación de los ficheros CSV oficiales (2023–2025) y auditoría de versiones del entorno.
Fase 2 — Comprensión de Datos (EDA): Análisis temporal (mensual e intradiario bimodal), espacial por distritos, tipológico vehicular y control de calidad.
Fase 3 — Preparación e Ingeniería Espaciotemporal: Filtrado UTM, exclusión de variables post-siniestro (data leakage), consolidación por `num_expediente`, generación de malla $100 \times 100\text{ m}$ y muestreo controlado $1:3$.
Fase 4 y 4.1 — Modelado y Extensión Híbrida: Entrenamiento y ajuste (`GridSearchCV` + `sample_weight`) de XGBoost Base, Regresión Logística, Baseline Histórico, XGBoost Híbrido y validación cruzada temporal estricta Out-of-Fold.
Fase 5 — Evaluación y Agregación Espacial: Cálculo del Riesgo Acumulado Anual, aplicación del umbral Percentil 95 de entrenamiento y extracción de importancias globales.
Fase 6 — Despliegue Business Intelligence: Conversión geoespacial `ETRS89 UTM 30N -> WGS84 (Lat/Lon)`, exportación de modelo en estrella para Power BI (6 tablas CSV + medidas DAX) y generación de cuadro de mando.
Fase 7 — Comparativa Global de Robustez: Consolidación multidimensional de los 5 modelos frente a las 5 métricas, matrices de confusión y diferenciales netos en Test 2025.
---
⚙️ Requisitos y Reproducibilidad
```bash
pip install numpy pandas matplotlib seaborn scikit-learn xgboost plotly ipywidgets
```
Semilla global: `RANDOM_STATE = 42`
Datos abiertos originales: Portal de Datos Abiertos del Ayuntamiento de Madrid — Accidentes de tráfico
