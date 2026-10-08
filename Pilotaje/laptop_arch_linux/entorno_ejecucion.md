# Entorno de ejecución — Laptop (Arch Linux)

Capturado automáticamente antes de ejecutar el piloto, para que cualquier persona sin
conocimiento previo del proyecto pueda reproducir exactamente este entorno.

| Campo | Valor |
|---|---|
| Fecha/hora de captura | 2026-10-04T20:54:20 (hora local) |
| Sistema operativo | Linux-7.1.11-arch1-1-x86_64-with-glibc2.44 (Arch Linux) |
| Kernel | 7.1.11-arch1-1 |
| Arquitectura | x86_64 |
| Python | 3.14.7 (CPython) |
| CPUs lógicas | 12 |
| RAM total | 31.0 GB |
| RAM disponible al momento de la captura | 16.0 GB |
| GPU | No detectada / no requerida (Protocolo v2.1 §10: GPU no requerida) |
| Espacio en disco (partición del proyecto) | 295 GB totales, 36 GB libres al momento de la captura |
| Commit de git al iniciar el piloto | `a7c0f4f` |

## Cómo se construyó el entorno

```bash
cd "Proyecto Github"
python3 -m venv .venv
.venv/bin/pip install --upgrade pip
.venv/bin/pip install -r requirements.txt
.venv/bin/pip freeze > Pilotaje/laptop_arch_linux/requirements_lock.txt
```

`requirements.txt` (raíz del repo) es la lista declarativa de dependencias, igual para
cualquier entorno. `requirements_lock.txt` (en esta carpeta) es el `pip freeze` exacto
de **esta máquina en este momento** — las versiones realmente resueltas por pip, no un
rango. Si se reproduce en otra máquina con el mismo `requirements.txt` pero en otro
momento, pip podría resolver versiones distintas; para reproducir exactamente este
piloto, instalar desde `requirements_lock.txt`:

```bash
.venv/bin/pip install -r Pilotaje/laptop_arch_linux/requirements_lock.txt
```

## Nota sobre Python 3.14

Es una versión reciente del intérprete. Antes de instalar se verificó (dry-run) que
todas las dependencias del protocolo (`scikit-learn`, `xgboost`, `lightgbm`, `shap`,
`imbalanced-learn`) publican wheels compatibles con `cp314` en PyPI — no fue necesario
fijar una versión de Python distinta a la del sistema.

## Discrepancia con Protocolo v2.1 §10

El protocolo declara como entorno de cómputo "PC de escritorio (Windows 10) en local,
con conda/venv dedicado; Colab como respaldo puntual". Esta ejecución ocurre en la
laptop (Arch Linux), no en la PC de escritorio Windows. Registrado como `INC-01` en
`../incidencias_log.csv`. El piloto se repetirá en la PC de escritorio
(`../escritorio_windows10/`) para decidir si el §10 del protocolo debe actualizarse a
reflejar ambos entornos.

## Medición de recursos durante la ejecución

Cada paso pesado del notebook (carga del dataset, entrenamiento del baseline) registra
tiempo de ejecución y uso de memoria pico en la bitácora (`bitacora_ejecucion.csv`,
columna `evidencia`), usando `time.perf_counter()` y `resource.getrusage` dentro del
propio notebook — no una herramienta externa — para que la medición quede versionada
junto con el resultado que midió.
