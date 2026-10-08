# Índice de trazabilidad — Cronograma Tesis II

Mapeo entre fases del cronograma (`Trabajos del Curso/Cronograma Tesis II...`), semanas, entregables y su ubicación en este repositorio.

| Fase | Semana | Entregable | Carpeta en el repo | Estado |
|---|---|---|---|---|
| 1. Planificación | 2 | Protocolo de investigación v2.1 (protocolo experimental §11 + matriz de trazabilidad §12) | `DOCUMENTOS ELABORADOS DE LA TESIS/Protocolo de Investigacion v2.1.md` (local, no versionado) | ✅ Cerrado — P08 y P13 resueltos por criterio propio (sin asesora en Seminario de Tesis II), fundamentados en Dietterich 1998, Demšar 2006, Raschka 2018 |
| 2. Adquisición | 3 | Dataset crudo verificado | `Laboratorio de Pruebas/Fase2_Adquisicion_Preparacion/` → genera `data/raw/dataset_crudo_uci697.csv` | 🔲 Pendiente de ejecutar |
| 2. EDA | 3-4 | Informe EDA + `dataset_metadata.csv` + `dataset_audit.csv` (Protocolo v2.1, P01-P02) | `Laboratorio de Pruebas/Fase2_Adquisicion_Preparacion/01_Adquisicion_EDA.ipynb` → genera `data/processed/` | 🔲 Piloto (P01-P03) en curso — ver `Pilotaje/` |
| 2. Depuración | 4 | Dataset binario depurado (3,630 registros) | `Laboratorio de Pruebas/Fase2_Adquisicion_Preparacion/` → genera `data/processed/` | ⬜ No iniciado |
| 3. Partición | 5 | Dataset experimental congelado | `Laboratorio de Pruebas/Fase3_Preprocesamiento/` | ⬜ No iniciado |
| 3. Preprocesamiento | 6 | Pipeline de preprocesamiento reproducible | `Laboratorio de Pruebas/Fase3_Preprocesamiento/` | ⬜ No iniciado |
| 4. Baseline | 7 | Regresión Logística reproducible | `Laboratorio de Pruebas/Fase4_Entrenamiento/baseline_regresion_logistica/` | ⬜ No iniciado |
| 4. Random Forest | 8 | Modelo RF ajustado (Optuna, k=5) | `Laboratorio de Pruebas/Fase4_Entrenamiento/random_forest/` | ⬜ No iniciado |
| 4. XGBoost | 9 | Modelo XGBoost ajustado (Optuna, k=5) | `Laboratorio de Pruebas/Fase4_Entrenamiento/xgboost/` | ⬜ No iniciado |
| 4. LightGBM | 10 | Modelo LightGBM ajustado (Optuna, k=5) | `Laboratorio de Pruebas/Fase4_Entrenamiento/lightgbm/` | ⬜ No iniciado |
| 5. Evaluación | 11 | Tabla de resultados congelada (test, uso único) | `Laboratorio de Pruebas/Fase5_Evaluacion/` | ⬜ No iniciado |
| 6. Supuestos + comparación | 12-13 | Shapiro-Wilk, Friedman/Nemenyi, Wilcoxon/McNemar | `Laboratorio de Pruebas/Fase6_Validacion_Analisis/` | ⬜ No iniciado |
| 6. Interpretabilidad | 14 | Informe SHAP | `Laboratorio de Pruebas/Fase6_Validacion_Analisis/` | ⬜ No iniciado |
| 6. Transferencia | 15 | Protocolo de replicación UNAJ v1 | `Laboratorio de Pruebas/Fase6_Validacion_Analisis/` | ⬜ No iniciado |
| Cierre | 16-17 | Borrador integral / Tesis final + PPT | `DOCUMENTOS ELABORADOS DE LA TESIS/` (local, no versionado) | ⬜ No iniciado |

**Leyenda:** ✅ Cerrado · 🔲 En curso / próximo paso inmediato · ⬜ No iniciado

**Convención de datos:** `data/raw/` y `data/processed/` no se versionan en git (dataset regenerable desde el notebook de adquisición, ver `.gitignore`). Cada notebook de fase referencia las rutas relativas correspondientes.

**Convención de entorno:** un solo `requirements.txt` en la raíz para todo el pipeline (ver pasos de configuración en Windows 10 más abajo o en `Apuntes semana 2.md`).
