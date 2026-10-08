# Informe de Pilotaje — Laboratorio Semana 4

## Datos (ficha del curso)

| Campo | Completar |
|---|---|
| **Tesista** | Darío Daniel Quispe Quispe |
| **Título de tesis** | "Predicción de deserción estudiantil universitaria mediante Machine Learning en la Universidad Nacional de Juliaca" |
| **Tipo** | ☑ IA/ML · ☐ Software/Sistemas · ☐ Usuarios/Instrumentos · ☐ Otro |
| **Protocolo a ejecutar** | v2.1 (`DOCUMENTOS ELABORADOS DE LA TESIS/Protocolo de Investigacion v2.1.md`) |
| **Fecha de ejecución** | 2026-10-04 |
| **Docente** | Dra. Liz Huancapaza Hilasaca — Seminario de Tesis II, 2026-II |
| **Entorno de ejecución** | laptop (Arch Linux) — ver `laptop_arch_linux/entorno_ejecucion.md` |
| **Semáforo del piloto** | 🟡 **AMARILLO** |

> **Nota sobre el versionado del protocolo.** La ficha del curso nombra las versiones
> como "V1.1 → V1.2". Este proyecto usa su propio esquema de versionado semántico: la
> versión ejecutada en el piloto es la **v2.1** y la versión que incorporará las
> correcciones derivadas del piloto será la **v2.2**. Por tanto, **donde la ficha dice
> "Protocolo V1.2", en este proyecto corresponde a la v2.2** (ver Etapa F). No se trata
> de una versión distinta ni equivocada, sino de la misma mecánica de versionado con otra
> numeración, consistente con todo el historial del protocolo.

**Evidencia complementaria de este informe:**
- `Notebooks_Ejecutados.md` — recorrido de los 4 notebooks con las figuras e imágenes generadas.
- `registro_cambios_preparacion.md` — 13 cambios de preparación, clasificados.
- `incidencias_log.csv` + `INC-02_discrepancia_variables.md` — incidencias detectadas.
- `laptop_arch_linux/bitacora_ejecucion.csv` — bitácora completa, generada automáticamente.
- `laptop_arch_linux/evidencias/` — todos los archivos de evidencia citados en este informe.

---

## A. Identificación y objetivo del piloto

Este piloto corresponde al Laboratorio de la Semana 4 de Seminario de Tesis II
("Ejecución del Piloto y Retroalimentación"). Su propósito, según el material del
curso, **no** es adelantar resultados de tesis, sino comprobar que el Protocolo v2.1 es
**ejecutable de punta a punta, claro, y produce evidencia trazable**, antes de la
ejecución definitiva (Fases 2-6 del cronograma).

Objetivo operacional de este piloto: ejecutar una parte representativa y mínima pero
completa del protocolo — desde la adquisición del dataset hasta una evaluación final
sobre un conjunto de prueba — usando una muestra reducida que no comprometa ni congele
prematuramente los recursos oficiales de la tesis (la partición 70/15/15 completa, que
corresponde a Fase 3, semana 5).

Como Seminario de Tesis II no asigna una asesora que valide cada decisión (a diferencia
de Tesis I), el criterio de cierre de las decisiones metodológicas de este piloto es del
propio tesista, apoyado en literatura y en el propio protocolo — tal como ya se aplicó
al cerrar el Protocolo v2.1 (seción de antecedentes, abajo).

### Etapa A — Diseño de la prueba mínima pero completa (respondido *antes* de ejecutar)

| Pregunta | Respuesta antes de ejecutar |
|---|---|
| ¿Qué objetivo específico está relacionado? | Principalmente **OE2** (comparar algoritmos de ML) e insumos para **OE1** (variables con poder predictivo). El piloto prueba que el procedimiento que sostiene esos objetivos es ejecutable. |
| ¿Qué parte exacta ejecutaré hoy? | El tramo **P01–P06 y P10** del Protocolo v2.1 §11: adquisición → auditoría → control de fuga → partición → preprocesamiento → baseline → evaluación final sobre test. |
| ¿Cuál será la entrada? | El dataset real (UCI id=697, 4,424 filas), del que se extrae una **muestra piloto estratificada de 1,200 filas** (no la partición oficial 70/15/15, reservada para Fase 3). |
| ¿Qué procedimiento aplicaré? | Pipeline del protocolo: exclusión de "Enrolled" + binarización, partición estratificada 70/15/15 con `random_state=42`, `StandardScaler` + `SMOTE` **solo sobre train**, entrenamiento de Regresión Logística (baseline) y predicción. |
| ¿Cuál será la salida observable? | Métricas (AUC-ROC, F1, precisión, recall, exactitud) sobre validación y test, matriz de confusión, y archivos de configuración/evidencia con sufijo `_piloto`. |
| ¿Qué evidencia guardaré? | CSV de muestra y partición, JSON de pipeline/hiperparámetros/métricas, figuras PNG del EDA, matrices de confusión y la bitácora `bitacora_ejecucion.csv` (ver Etapa B y sección D). |
| ¿Qué indicaría que el procedimiento NO está listo? | Un error de ejecución en cualquier notebook, una fuga de datos detectable (p. ej. SMOTE aplicado antes de partir), métricas incoherentes con la literatura del dataset, o una ficha técnica del protocolo que no coincida con los datos reales descargados. |

> El último criterio es el que efectivamente se activó: la ficha técnica del §5 **no
> coincidió** con los datos reales (36 vs. 35 variables) → ver INC-02. El piloto cumplió
> su función de disparador temprano.

---

## B. Versión y parte del protocolo probada

**Versión probada:** Protocolo v2.1 (la versión vigente; no existe una versión posterior
específica para el piloto — ver sección G sobre por qué el protocolo no cambia de
versión a raíz de este piloto).

### Matriz de cobertura — qué partes del protocolo se ejercitaron

| Paso (§11) | Descripción | ¿Se ejecutó en este piloto? | Cómo / evidencia |
|---|---|---|---|
| P01 | Registro del dataset | ✅ Sí, completo | `dataset_metadata.csv` (notebook 01) |
| P02 | Auditoría inicial | ✅ Sí, completo | `dataset_audit.csv` (notebook 01) — encontró INC-02 |
| P03 | Control de fuga de información | ✅ Sí, completo | Verificación `student_id` único (notebook 01) |
| P04 | División de datos | ⚠️ Ejercitado, no oficial | Sobre muestra piloto (1,200 filas), no el dataset completo — `dataset_split_piloto.csv` |
| P05 | Preprocesamiento (encoding, escalado, SMOTE) | ⚠️ Ejercitado, no oficial | Sobre la muestra piloto (notebook 01, sección 8.3) |
| P06 | Baseline (Regresión Logística) | ⚠️ Ejercitado, no oficial | Notebook 02, sobre muestra piloto |
| P07 | Modelos de ensamble (RF/XGBoost/LightGBM) + Optuna | ❌ No — Fase 4 (semana 8-10) | Se corrió un **extra no numerado**: Random Forest sin tuning (notebook 03), solo para probar que el pipeline es agnóstico al algoritmo |
| P08 | Repeticiones y control de aleatoriedad | ❌ No — aplica a Fase 6 | Decisión ya cerrada en el protocolo (v2.1, 5-fold×3 repeticiones); no había nada que ejecutar en el piloto |
| P09 | Registro de cada experimento | 🟡 Equivalente, no literal | No se generó `experimentos_log.csv`; la bitácora (`bitacora_ejecucion.csv`) cumple una función equivalente para el piloto |
| P10 | Evaluación final (test, uso único) | ⚠️ Ejercitado, no oficial | Notebook 04, sobre el test de la muestra piloto, una sola vez |
| P11 | Comparación estadística | ❌ No — Fase 6 | Fuera de alcance de un piloto de Semana 4 |
| P12 | Interpretabilidad (SHAP) | ❌ No — Fase 6 | Fuera de alcance |
| P13 | Análisis de errores | ❌ No ejecutado | Criterio ya definido en el protocolo (FN con probabilidad en [0.35, 0.50)); no se aplicó sobre resultados del piloto |
| P14 | Protocolo de replicación UNAJ (OE4) | ❌ No — Fase 6 | Fuera de alcance |
| P15 | Registro de incidencias y versionado | ✅ Sí, completo | `incidencias_log.csv`, `registro_cambios_preparacion.md` |
| P16 | Evidencias de reproducibilidad | ✅ Sí, completo | `entorno_ejecucion.md`, `requirements_lock.txt` |

**Lectura de la matriz:** el piloto cubrió bien el tramo P01-P06 y P10 (adquisición →
auditoría → fuga → partición → preprocesamiento → entrenamiento → evaluación final), que
es exactamente el tramo que un piloto de Semana 4 debe probar según el material del
curso ("carga → preprocesamiento → entrenamiento reducido → predicción → métrica →
guardado"). Los pasos marcados ⚠️ se ejecutaron **sobre una muestra reducida, no
oficial** — ver sección C para el porqué. Los pasos marcados ❌ corresponden a fases
posteriores del cronograma (Fase 4 en adelante) y están correctamente fuera de alcance.

---

## C. Datos, muestra y condiciones utilizadas

### C.1 Dataset base

| Campo | Valor |
|---|---|
| Fuente | UCI Machine Learning Repository, id=697, DOI 10.24432/C5MC89 |
| Registros | 4,424 (0 discrepancia contra lo documentado) |
| Variables predictoras | **36** (no 35 como documenta el protocolo §5 — ver INC-02, sección E) |
| Clases originales | Dropout 32.12% / Graduate 49.93% / Enrolled 17.95% (coincide con protocolo, diferencia < 1pp) |
| Duplicados | 0 |
| Valores faltantes | 0 |
| Variables con >5 outliers (IQR) | 28 de 36 |

### C.2 Por qué una muestra piloto y no la partición oficial

El Protocolo v2.1 §11 (P04) exige que la partición 70/15/15 sobre el dataset completo se
ejecute **una sola vez y se congele** ("regla del test aislado") — y esa ejecución le
corresponde a Fase 3, semana 5, todavía no alcanzada en el cronograma. Ejecutarla ya,
dentro del piloto de Semana 4, la habría consumido antes de tiempo.

Por eso se construyó una **muestra piloto independiente**, exclusivamente para este
ejercicio:

1. Excluir "Enrolled" y binarizar (Dropout=1, Graduate=0) — igual que hará la ejecución
   oficial.
2. Tomar una muestra **estratificada** de **1,200 filas** del dataset depurado
   (3,630 filas tras excluir Enrolled) — estratificada por `class_binaria`, para
   preservar la proporción real (≈39.2% Dropout / 60.8% Graduate) y evitar que un
   recorte al azar introduzca ruido de muestreo que invalide la interpretación de las
   métricas.
3. Sobre esa muestra de 1,200, aplicar la **misma proporción** 70/15/15 que usará la
   partición oficial — también estratificada en cada corte — con `random_state=42` (la
   misma semilla que fija el protocolo para la partición real, por consistencia).
4. Todas las salidas quedan con sufijo `_piloto` y se guardan en
   `Pilotaje/laptop_arch_linux/evidencias/`, nunca en `data/processed/`, para que sea
   imposible confundir esta partición de ensayo con la oficial.

### C.3 Partición resultante

| Split | Filas | % |
|---|---|---|
| Train | 840 | 70.0% |
| Validación | 180 | 15.0% |
| Test | 180 | 15.0% |
| **Total (muestra piloto)** | **1,200** | **100%** |

### C.4 Preprocesamiento (P05)

Pipeline aplicado **solo sobre train**, tal como exige el protocolo:

1. `StandardScaler` — ajustado (`fit`) únicamente sobre train; validación y test solo
   se **transforman** con ese mismo escalador, nunca se reajustan.
2. `SMOTE(random_state=42)` — aplicado **solo** sobre train, nunca sobre validación ni
   test. Balanceó el train de 840 filas (≈329 Dropout / 511 Graduate) a **1,022 filas**
   (511/511).

Evidencia: `pipeline_config_piloto.json`.

### C.5 Entorno de cómputo

| Campo | Valor |
|---|---|
| Sistema operativo | Arch Linux (kernel 7.1.11-arch1-1) |
| Python | 3.14.7 (CPython) |
| CPUs / RAM | 12 núcleos / 31 GB |
| Entorno virtual | `.venv/` dedicado al proyecto, 134 paquetes con versión fija (`requirements_lock.txt`) |
| Commit de git al iniciar | `a7c0f4f` |

Detalle completo en `laptop_arch_linux/entorno_ejecucion.md`. Esta es la primera
discrepancia encontrada por el piloto: el Protocolo v2.1 §10 declara Windows 10 como
entorno — ver INC-01 en la sección E.

---

## D. Evidencias obtenidas

### Etapa B — Bitácora de ejecución (llenada *durante* la corrida, no al final)

Los 10 pasos ejecutados, con tiempo y memoria reales medidos por `psutil`. Fuente
completa (con RSS y delta de memoria por paso): `laptop_arch_linux/bitacora_ejecucion.csv`.

| ID | Acción | Entrada | Esperado | Observado | Inc. | Evidencia |
|---|---|---|---|---|---|---|
| P-01 | Excluir "Enrolled", binarizar y muestrear piloto estratificado | df completo (4,424 filas, 3 clases) | ~1,200 filas, 2 clases, proporción similar | OK · 0.031s | — | `dataset_muestra_piloto.csv` |
| P-02 | Particionar muestra piloto 70/15/15 estratificada | muestra piloto (1,200) | splits con proporción de clase preservada | OK · 0.012s | — | `dataset_split_piloto.csv` |
| P-03 | Ajustar pipeline (StandardScaler + SMOTE) solo sobre train | train piloto (840, 36 vars) | `fit` solo en train; val solo transformado | OK · 0.022s | — | `pipeline_config_piloto.json` |
| P-04 | Copiar evidencias P01–P03 al directorio del entorno | metadata + auditoría | ambos archivos en `Pilotaje/<entorno>/evidencias/` | OK · 0.001s | — | `dataset_metadata.csv`, `dataset_audit.csv` |
| P-05 | Reajustar StandardScaler + SMOTE sobre train | train piloto (840) | mismo resultado (determinista, mismo seed) | OK · 0.033s | — | (reusa `pipeline_config_piloto.json`) |
| P-06 | Entrenar baseline (Reg. Logística) y predecir sobre val | train tras SMOTE (1,022), val (180) | entrena sin error y produce predicciones | OK · 0.020s | — | `baseline_.../baseline_config_piloto.json`, `metricas_baseline_piloto.json` |
| P-07 | Reajustar pipeline (mismo que P03/P06) | train piloto (840) | mismo resultado (determinista) | OK · 0.036s | — | (reusa `pipeline_config_piloto.json`) |
| P-08 | Entrenar Random Forest (sin tuning) y predecir sobre val | train tras SMOTE (1,022), val (180) | entrena sin error con el mismo pipeline | OK · 0.165s | — | `random_forest/rf_config_piloto.json`, `metricas_rf_piloto.json` |
| P-09 | Reajustar pipeline y reentrenar ambos modelos | train piloto (840) | mismos modelos (determinista), sin tuning nuevo | OK · 0.197s | — | (evidencia = evaluación P-10) |
| P-10 | Evaluar ambos modelos sobre test piloto (única vez) | test piloto (180), 2 modelos | métricas + matriz de confusión por modelo, sin re-tocar test | OK · 0.335s | — | `metricas_test_piloto.json` + `matriz_confusion_test.png` por modelo |

**Lectura de la bitácora:** 10 pasos, **los 10 en estado OK, 0 errores**, 0 incidencias
de ejecución (las 3 incidencias de la sección E son de *contenido/entorno*, no fallas de
corrida). Los pasos de "reajuste" (P-05, P-07, P-09) son reentrenamientos deterministas
con la misma semilla al modularizar en notebooks separados — producen idéntico resultado,
confirmando reproducibilidad.

### D.1 Archivos de evidencia

Todas verificables en `laptop_arch_linux/evidencias/` (estructura completa y capturas en
`Notebooks_Ejecutados.md`):

| Archivo | Generado por | Qué contiene |
|---|---|---|
| `dataset_metadata.csv` | Notebook 01 (P01) | 4,424 filas: `student_id`, clase original, clase binaria, fuente |
| `dataset_audit.csv` | Notebook 01 (P02) | Resumen de auditoría: integridad, duplicados, faltantes, distribución, outliers |
| `dataset_muestra_piloto.csv` | Notebook 01 (§8.1) | Las 1,200 filas de la muestra piloto estratificada |
| `dataset_split_piloto.csv` | Notebook 01 (§8.2, P04) | Las 1,200 filas con columna `split` (train/val/test) |
| `pipeline_config_piloto.json` | Notebook 01 (§8.3, P05) | Pasos del pipeline, conteos antes/después de SMOTE, variables usadas |
| `figuras/distribucion_clases.png` | Notebook 01 | Distribución de las 3 clases originales |
| `figuras/outliers_boxplots.png` | Notebook 01 | Boxplots de las 36 variables numéricas |
| `figuras/matriz_correlacion.png` | Notebook 01 | Matriz de correlación entre variables numéricas |
| `comparacion_test_piloto.csv` | Notebook 04 (P10) | Métricas de ambos modelos sobre test, lado a lado |
| `baseline_regresion_logistica/baseline_config_piloto.json` | Notebook 02 (P06) | Hiperparámetros del baseline |
| `baseline_regresion_logistica/metricas_baseline_piloto.json` | Notebook 02 (P06) | Métricas sobre validación |
| `baseline_regresion_logistica/metricas_test_piloto.json` | Notebook 04 (P10) | Métricas sobre test |
| `baseline_regresion_logistica/matriz_confusion_test.png` | Notebook 04 (P10) | Matriz de confusión sobre test |
| `random_forest/rf_config_piloto.json` | Notebook 03 | Hiperparámetros del RF (por defecto, sin tuning) |
| `random_forest/metricas_rf_piloto.json` | Notebook 03 | Métricas sobre validación |
| `random_forest/metricas_test_piloto.json` | Notebook 04 (P10) | Métricas sobre test |
| `random_forest/matriz_confusion_test.png` | Notebook 04 (P10) | Matriz de confusión sobre test |
| `bitacora_ejecucion.csv` | Los 4 notebooks | 10 pasos (P-01 a P-10), con tiempo y memoria reales por paso |

### D.2 Resultados preliminares — validación

| Modelo | AUC-ROC | F1 | Precisión | Recall | Exactitud |
|---|---|---|---|---|---|
| Regresión Logística (baseline) | 0.9633 | 0.8652 | 0.8714 | 0.8592 | 0.8944 |
| Random Forest (sin tuning) | 0.9545 | 0.8429 | 0.8551 | 0.8310 | 0.8778 |

### D.3 Resultados preliminares — test (evaluación final única, P10)

| Modelo | AUC-ROC | F1 | Precisión | Recall | Exactitud |
|---|---|---|---|---|---|
| Regresión Logística (baseline) | 0.9301 | 0.8630 | 0.8289 | **0.9000** | 0.8889 |
| Random Forest (sin tuning) | 0.9451 | 0.8529 | 0.8788 | 0.8286 | 0.8889 |

**Lectura preliminar (no es conclusión de tesis):** en test, el baseline obtuvo mayor
recall de Dropout (0.900 vs. 0.829) que Random Forest sin tuning — es decir, para esta
muestra reducida y sin ajuste de hiperparámetros, el modelo más simple detectó mejor a
los estudiantes que realmente desertan, que es la métrica que el Protocolo v2.1 §7
prioriza como criterio de desempate. Esto es consistente con que RF sin tuning no tiene
ventaja estructural sobre un modelo lineal en una muestra chica — la comparación real
(con tuning, los 4 modelos, dataset completo) es la que hará Fase 4-6.

### D.4 Vínculo con los objetivos específicos

- **OE1** (variables con mayor poder predictivo): la auditoría y el EDA (figuras de
  distribución, outliers y correlación) dan insumos reales, aunque la selección formal
  de variables (más allá de usarlas todas) no es parte de este piloto.
- **OE2** (comparar algoritmos): parcialmente cubierto — se probó el pipeline con 2 de
  los 4 algoritmos previstos (baseline + Random Forest, sin tuning), confirmando que el
  mecanismo de entrenamiento/evaluación es agnóstico al algoritmo. La comparación formal
  (los 4 modelos, con tuning, sobre partición oficial) es Fase 4-6.
- **OE3** (SHAP) y **OE4** (protocolo de replicación UNAJ): fuera de alcance de este
  piloto, corresponden a Fase 6.

---

## E. Incidencias y análisis

Tres incidencias reales, encontradas al ejecutar (no al redactar), registradas en
`incidencias_log.csv`:

### INC-01 — Entorno de cómputo no coincide con el protocolo

El Protocolo v2.1 §10 declara "PC de escritorio (Windows 10) en local, con conda/venv
dedicado". El piloto se ejecutó en una laptop con Arch Linux. **Tipo:** metodológica.
**¿Afecta validez?** Sí, en el sentido de que el protocolo describe un entorno que no es
el que efectivamente se usó. **Decisión (tomada y ejecutada): Corregir** — declarar ambos entornos
(Windows 10 y Arch Linux) en el §10 del Protocolo v2.2. 
*Actualización (2026-10-08):* Piloto ejecutado en la PC de escritorio (Windows 10, carpeta `escritorio_windows10/`), confirmando paridad exacta de métricas con Arch Linux. **INC-01 Cerrada.**

### INC-02 — El dataset trae 36 variables predictoras, no 35

`dataset_audit.csv` reportó 36 variables, no las 35 que documenta el Protocolo v2.1 §5.
**Tipo:** técnica. Investigado a fondo (ver `INC-02_discrepancia_variables.md`) con 3
fuentes independientes:

1. CSV crudo descargado: 37 columnas (36 predictoras + `Target`).
2. Metadatos de `ucimlrepo`: `num_features = 36`.
3. Página oficial del UCI Machine Learning Repository: declara explícitamente 36
   features.

**Origen:** el paper original (Realinho et al., 2022, *Data*, 7(11), 146) describe
35 atributos en su abstract — pero el dataset que efectivamente sirve el repositorio UCI
(de donde se descarga el dato real, vía `ucimlrepo` id=697) trae 36. Es una divergencia
real entre la publicación citable y la versión actual del repositorio, no un error de
nuestra descarga ni de nuestro código. La variable candidata a ser la añadida
posteriormente es `International` (flag binario, redundante conceptualmente con
`Nacionality`), aunque esto **no se pudo confirmar al 100%** por no poder extraer de
forma confiable la tabla de variables del PDF original.

**¿Afecta validez?** Sí, en el sentido de que la ficha técnica documentada no coincide
con los datos reales usados — es exactamente el tipo de observación que un jurado podría
hacer en la defensa si se detecta tarde. **Decisión (tomada): Corregir** — actualizar el
§5 de 35 a 36 variables y emitir el Protocolo v2.2 (según la regla de versionado P15).
**Ejecución agendada** antes de Fase 3. No afecta el piloto en sí: el pipeline ya usa las
36 variables reales, independientemente de lo que diga el §5.

### INC-03 — El entorno Linux obliga a usar un entorno virtual

Arch Linux bloquea `pip install` a nivel de sistema (PEP 668,
*externally-managed-environment*). **Tipo:** técnica. **¿Afecta validez?** No.
**Decisión:** se creó un venv dedicado en la raíz del repositorio (`.venv/`), que de
todas formas ya exigía el Protocolo v2.1 §10 ("conda/venv dedicado") — no requirió
cambiar nada del protocolo, solo confirmó que el venv no es opcional en este entorno.
Resuelta en el momento.

---

## F. Decisiones y cambios

### Etapa F — Decisiones para el Protocolo v2.2 (lo que la ficha del curso llama "V1.2")

Decisiones **tomadas** a partir de la evidencia del piloto (no quedan "pendientes": lo que
se agenda es la *ejecución* de cada acción, no la decisión en sí):

| Elemento | Decisión | Justificación basada en evidencia | Acción concreta | Sección |
|---|---|---|---|---|
| Conteo de variables predictoras (INC-02) | ☑ **Corregir** | `dataset_audit.csv` + 3 fuentes independientes (CSV crudo, `ucimlrepo`, página oficial UCI) confirman **36**, no 35. El pipeline ya usa 36. | Actualizar la ficha técnica de 35 → **36 variables** y emitir **Protocolo v2.2**. | §5 |
| Entorno de cómputo declarado (INC-01) | ☑ **Corregir** | El §10 declara solo Windows 10; el piloto corrió en Arch Linux sin incidencias de validez. La reproducibilidad exige declarar el entorno real. | Declarar **ambos entornos** (Windows 10 y Arch Linux) en el §10; ejecutar el mismo piloto en `escritorio_windows10/` para confirmar paridad. | §10 |
| Partición 70/15/15 estratificada con `random_state=42` y regla del test aislado | ☑ **Mantener** | Corrió limpia, preservó la proporción de clases en cada split; ninguna fuga detectada. | Ninguna — se confirma para la ejecución oficial de Fase 3. | §11 (P04, P10) |
| Pipeline de preprocesamiento: `StandardScaler` + `SMOTE` **solo sobre train** | ☑ **Mantener** | Verificado sin fuga de datos; métricas consistentes con la literatura del dataset (benchmark F1≈0.904, Romero et al. 2025). | Ninguna. | §11 (P05) |
| Recall de *Dropout* como criterio de desempate entre modelos | ☑ **Mantener** | El baseline alcanzó recall 0.900 en test; la métrica prioriza correctamente detectar al que deserta. | Ninguna. | §7 |

> **Decisión tomada ≠ ejecución completada.** Las dos correcciones (§5 y §10) están
> **decididas** con justificación y sección identificada; su ejecución (emitir el documento
> v2.2 y correr el entorno Windows) está agendada **antes de Fase 3 / Semana 5**. Ver
> sección G para el estado formal del documento del protocolo.

### Registro de cambios de preparación

Registro completo en `registro_cambios_preparacion.md` (13 cambios, C-01 a C-13),
clasificados técnico/metodológico. Resumen por categoría:

**Preparación de repositorio y entorno (técnico, no toca el protocolo):**
- C-01 a C-04: corrección de referencias (notebook, índice de cronograma, README) a la
  versión vigente del protocolo (v2.1); `psutil` agregado como dependencia para medir
  recursos.
- C-05, C-06: carpeta `Pilotaje/` con estructura por entorno; entorno virtual dedicado
  con versiones fijas (`requirements_lock.txt`).

**Ejecución del piloto en sí (metodológico, acotado — no modifica el protocolo):**
- C-07: sección 8 del notebook 01 — P04-P05 sobre muestra piloto, no la partición
  oficial.
- C-08: bitácora instrumentada automáticamente (tiempo + memoria reales por paso, vía
  `psutil`, multiplataforma).
- C-09: figuras del EDA exportadas como PNG, no solo embebidas en el notebook.
- C-10, C-11: modularización del entrenamiento en notebooks separados por modelo
  (`02`, `03`), con evidencia organizada en subcarpetas por modelo.
- C-12: corrección de un bug real encontrado al modularizar — el contador de IDs de la
  bitácora se reiniciaba en cada notebook y habría generado IDs duplicados (`P-01`
  repetido 3 veces); se corrigió para que continúe la numeración leyendo el archivo
  existente.
- C-13: notebook 04 — evaluación final única sobre test piloto (P10), con matriz de
  confusión por modelo.

Ningún cambio modificó el contenido del Protocolo v2.1. Las dos decisiones que sí podrían
requerir tocarlo (INC-01, INC-02) están identificadas y pendientes a propósito.

---

## G. Estado del protocolo

El piloto **sí genera una nueva versión del protocolo**: la decisión tomada en la Etapa F
es corregir dos puntos de la ficha técnica (§5, conteo de variables; §10, entorno de
cómputo), lo que da lugar al **Protocolo v2.2** — la versión que la ficha del curso
denomina "V1.2". Ambos son cambios de *documentación* (no alteran ningún procedimiento de
cálculo), por lo que la mecánica de experimentación validada en el piloto se mantiene
intacta; la numeración sube siguiendo la propia regla del protocolo (P15: "si hay una
desviación respecto a lo documentado, se emite una nueva versión indicando qué cambió,
cuándo y por qué").

**Qué cambia de v2.1 a v2.2:**

| Sección | v2.1 (ejecutada en el piloto) | v2.2 (corregida) | Origen |
|---|---|---|---|
| §5 | 35 variables predictoras | **36 variables predictoras** | INC-02 |
| §10 | Entorno: Windows 10 | **Windows 10 y Arch Linux** (ambos declarados) | INC-01 |

**Estado de emisión del documento v2.2:** las correcciones están **decididas y
especificadas** (arriba); la emisión del archivo `Protocolo de Investigacion v2.2.md` y la
corrida de confirmación en Windows están agendadas **antes de Fase 3 / Semana 5**. Nada de
esto bloquea continuar con el EDA formal de Fase 2 mientras tanto, pero debe cerrarse antes
de congelar la partición oficial 70/15/15.

---

## H. Prueba de trazabilidad (Etapa D de la Ficha del curso)

Ejercicio de reconstrucción: ¿de dónde sale el **F1 = 0.8630** del baseline sobre test
(sección D.2)?

| Pregunta | Respuesta |
|---|---|
| ¿Qué dato/muestra lo produjo? | `dataset_split_piloto.csv`, filas con `split == "test"` (180 filas) |
| ¿Qué versión del protocolo? | v2.1, pasos P04 (partición), P05 (pipeline), P06 (baseline), P10 (evaluación final) |
| ¿Qué configuración/parámetros? | `baseline_regresion_logistica/baseline_config_piloto.json` → `LogisticRegression(max_iter=1000, random_state=42)`, con SMOTE sobre train |
| ¿Qué código lo produjo? | `04_Piloto_Evaluacion_Test.ipynb`, sección 3 (reentrena sobre train, predice sobre test una sola vez) |
| ¿Dónde está la evidencia? | `baseline_regresion_logistica/metricas_test_piloto.json` y `matriz_confusion_test.png` |
| ¿Se podría repetir mañana? | Sí: `requirements_lock.txt` fija las 134 versiones exactas de librerías, `random_state=42` está fijo en cada paso, y el commit de git (`a7c0f4f`) identifica el código exacto |

La cadena es reconstruible sin ambigüedad, de punta a punta.

---

## I. Semáforo y síntesis

**Semáforo: 🟡 AMARILLO** — el pipeline corrió limpio (0 errores en las 4 notebooks, 10
pasos de bitácora todos en "OK"), pero quedan 2 incidencias reales sin resolver que tocan
la ficha técnica del protocolo (INC-01, INC-02): se ejecutó, pero requiere correcciones
antes de la ejecución definitiva. No es rojo porque ninguna incidencia impide continuar.

Las 12 preguntas de síntesis completas están en la Sección 9 de
`01_Adquisicion_EDA.ipynb` (reproducidas aquí de forma resumida):

1. **Qué funcionó:** integridad del dataset, distribución de clases, unicidad de
   `student_id` — todo verificado contra lo documentado.
2. **Qué fue ambiguo:** el protocolo no preveía un entorno distinto a Windows 10, ni
   verificaba la ficha técnica contra la copia real descargada.
3. **Decisión no definida que hubo que tomar:** usar una muestra piloto en vez de la
   partición oficial completa, y modularizar el entrenamiento en notebooks separados.
4. **¿La evidencia responde a los objetivos?** Parcial — sí para OE1 (insumos de EDA),
   parcial para OE2 (se probó el pipeline con 2 de 4 algoritmos, sin tuning).
5. **Problemas de datos:** ninguno en calidad (0 duplicados/faltantes); 28 variables con
   outliers IQR a vigilar en Fase 3; la discrepancia de variables es de documentación,
   no de calidad.
6. **¿Las métricas se pudieron aplicar?** Sí, sin problema, con las librerías estándar.
7. **¿Riesgo de fuga/sesgo?** No detectado — SMOTE solo en train, verificado.
8. **Qué definir mejor:** resolver INC-01 e INC-02 antes de Fase 3.
9. **Qué no debe cambiar:** la partición 70/15/15 con seed=42, y el recall de Dropout
   como criterio de desempate.
10. **Qué preguntaría otra persona:** solo el estado de INC-01/INC-02 — el resto es
    reproducible sin preguntas.
11. **Error que pudo haber comprometido la tesis si se detectaba después:** INC-02.
12. **¿El cronograma sigue viable?** Sí, sin mover fechas; se agrega una micro-tarea
    antes de Fase 3.

---

## Conclusión y próximos pasos

El piloto demuestra que el Protocolo v2.1 es ejecutable de punta a punta — adquisición,
auditoría, partición, preprocesamiento, entrenamiento (2 algoritmos) y evaluación final
sobre test — con evidencia trazable y reconstruible en cada paso. Encontró dos
incidencias reales (una metodológica, una técnica/de documentación) antes de que fueran
costosas, que es precisamente el objetivo de un piloto.

**Antes de Fase 3 (semana 5)** — ejecutar las decisiones ya tomadas en la Etapa F:
1. **INC-01:** declarar ambos entornos (Windows 10 + Arch Linux) en el §10 y correr el
   mismo piloto en `escritorio_windows10/` para confirmar paridad.
2. **INC-02:** actualizar el §5 de 35 a 36 variables y **emitir el Protocolo v2.2**
   (= "V1.2" de la ficha), que recoge ambas correcciones.
3. Nada de esto bloquea continuar con el EDA formal de Fase 2 mientras tanto.
