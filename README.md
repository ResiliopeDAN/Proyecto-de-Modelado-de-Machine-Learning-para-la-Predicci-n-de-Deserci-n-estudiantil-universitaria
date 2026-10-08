# Predicción de deserción estudiantil universitaria — UNAJ

Tesis de Ingeniería de Sistemas y Software (Seminario de Tesis II).

**Autor:** Darío Daniel Quispe Quispe
**Docente:** Dra. Liz Huancapaza Hilasaca
**Facultad:** Ciencias de la Ingeniería — Ingeniería de Sistemas y Software, UNAJ

## Objetivo

Desarrollar un modelo predictivo basado en Machine Learning que identifique de forma temprana a estudiantes con alto riesgo de deserción, usando variables académicas y socioeconómicas del dataset público **UCI id=697** ("Predict Students' Dropout and Academic Success", Realinho et al. 2022, DOI 10.24432/C5MC89), comparando Regresión Logística (baseline), Random Forest, XGBoost y LightGBM, con interpretabilidad vía SHAP. El objetivo final es un protocolo metodológico replicable sobre datos institucionales.

Desarrollo en 17 semanas según `INDICE_CRONOGRAMA.md`.

## Estado de avance (trazabilidad por fase)

Ver el detalle completo, con carpeta y entregable de cada fase, en **[`INDICE_CRONOGRAMA.md`](./INDICE_CRONOGRAMA.md)**.

| Fase | Semana | Estado |
|---|---|---|
| 1. Planificación — Protocolo de investigación v2.1 | 2 | ✅ Cerrado (contenido de fondo v1 + capa operativa v2 + P08/P13 cerrados en v2.1) |
| 2. Adquisición del dataset + EDA | 3-4 | 🔲 En curso — piloto Semana 4 en `Pilotaje/` |
| 3. Preprocesamiento (partición, pipeline) | 5-6 | ⬜ No iniciado |
| 4. Entrenamiento (baseline, RF, XGBoost, LightGBM) | 7-10 | ⬜ No iniciado |
| 5. Evaluación (test, uso único) | 11 | ⬜ No iniciado |
| 6. Validación estadística e interpretabilidad (SHAP) | 12-15 | ⬜ No iniciado |
| Cierre | 16-17 | ⬜ No iniciado |

## Estructura del repositorio

```
Proyecto Github/
├── INDICE_CRONOGRAMA.md              Trazabilidad fase → semana → entregable → carpeta
├── requirements.txt                  Dependencias del entorno (una sola vez para todo el pipeline)
├── Laboratorio de Pruebas/           Notebooks de trabajo, una carpeta por fase del cronograma
│   ├── Fase2_Adquisicion_Preparacion/
│   ├── Fase3_Preprocesamiento/
│   ├── Fase4_Entrenamiento/{baseline_regresion_logistica,random_forest,xgboost,lightgbm}/
│   ├── Fase5_Evaluacion/
│   └── Fase6_Validacion_Analisis/
├── Pilotaje/                         Piloto Semana 4: ejecución reducida (P01-P06) en dos entornos
│   ├── laptop_arch_linux/            Entorno, evidencias y bitácora — laptop (Arch Linux)
│   └── escritorio_windows10/         Entorno, evidencias y bitácora — PC de escritorio (Windows 10)
├── data/                             Dataset crudo/procesado (no versionado, se regenera con el notebook de Fase 2)
└── DOCUMENTOS ELABORADOS DE LA TESIS/  Protocolo, borradores de capítulos (local, no se sube al repositorio)
```

Los documentos formales de la tesis (protocolo, borradores) se mantienen fuera del repositorio público por decisión del autor; este repositorio versiona únicamente el trabajo técnico reproducible (código, notebooks, configuración de entorno).

## Cómo ejecutar el pipeline

El proyecto se trabaja en dos máquinas (laptop Arch Linux y PC de escritorio Windows 10)
— ver `Pilotaje/README.md` para el detalle de por qué y cómo se documenta cada una.

**Linux / macOS (bash):**

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m ipykernel install --user --name=tesis-desercion --display-name "Tesis Deserción UNAJ"
```

**Windows (PowerShell):**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
python -m ipykernel install --user --name=tesis-desercion --display-name "Tesis Deserción UNAJ"
```

Luego abrir el notebook de la fase correspondiente dentro de `Laboratorio de Pruebas/` con el kernel `tesis-desercion`. Sin requerimiento de GPU — los 4 algoritmos entrenan en CPU en minutos sobre este dataset (~3,630 registros tras depuración).

Cada máquina genera su propio `requirements_lock.txt` (versiones exactas resueltas por
pip en ese entorno) dentro de `Pilotaje/<entorno>/` — ver esas carpetas para reproducir
exactamente lo que corrió ahí.
