# Comparación de Entornos — Pilotaje (Semana 4)

Este documento cierra el análisis del pilotaje demostrando la reproducibilidad del pipeline en dos entornos operativos distintos (Arch Linux y Windows 10), tal como se decidió al abrir la incidencia **INC-01**.

## 1. Diferencias de Entorno Base

| Componente | Laptop (Arch Linux) | PC Escritorio (Windows 10) |
|---|---|---|
| **Sistema Operativo** | Linux-7.1.11-arch1-1 | Windows-10-10.0.19045-SP0 |
| **Intérprete Python** | 3.14.7 | 3.13.1 |
| **CPUs / RAM** | 12 núcleos / 31 GB | 16 núcleos / 32 GB |

## 2. Obstáculos Encontrados en Windows y Cómo se Abordaron

Durante la ejecución en la PC de escritorio, surgieron retos técnicos que no se presentaron en Arch Linux:

### A. Fallo de Descarga por Certificado SSL (INC-04)
* **El Obstáculo:** Al ejecutar el primer notebook (`01_Adquisicion_EDA.ipynb`), el proceso se interrumpió con el error fatal `SSL: CERTIFICATE_VERIFY_FAILED`. La instalación de Python 3.13 en Windows no disponía de un *bundle* de certificados CA actualizado para validar el certificado del servidor de UCI — un problema conocido de instalaciones de Python en Windows, no del proyecto.
* **Cómo se abordó (workaround de infraestructura):** se creó un `sitecustomize.py` dentro del entorno virtual (`.venv\Lib\site-packages\sitecustomize.py`) que fuerza globalmente `ssl._create_unverified_context()`, permitiendo completar la descarga sin modificar ni una línea de los notebooks `.ipynb` oficiales.
* **Limitaciones de este workaround (declaradas con honestidad):**
    * Desactiva la verificación de certificados en *todo* el entorno. Es aceptable para una descarga puntual de un dataset público, pero **no es una práctica recomendable** como solución permanente.
    * El `sitecustomize.py` vive dentro de `.venv/`, que **no se versiona** (`.gitignore`). Por tanto el parche **no es reproducible desde el repositorio**: una clonación limpia en otra máquina Windows volvería a encontrar el mismo error. Mover el parche al venv evita tocar los notebooks, pero **no lo convierte en "reproducibilidad limpia"** — al contrario, lo vuelve invisible y no versionado.
* **Causa raíz (confirmada en el código de `ucimlrepo`):** la librería hace dos peticiones.
  El **metadata (JSON)** sí usa `certifi` (`ssl.create_default_context(cafile=certifi.where())`)
  y funcionó; pero los **datos (CSV)** se bajan con `pandas.read_csv(data_url)`, que delega en
  `urllib` con el **contexto por defecto** → en Windows usa el **almacén de CA del sistema
  operativo** (no `certifi`), que estaba desactualizado. Por eso actualizar solo `certifi` no
  basta.
* **Fix correcto (implementado y VERIFICADO, verificación SSL activada):** declarar
  `SSL_CERT_FILE` apuntando al *bundle* de `certifi` antes de ejecutar, para que el contexto
  por defecto de `urllib` (el que usa pandas) también use `certifi`:
  `$env:SSL_CERT_FILE = .venv\Scripts\python.exe -c "import certifi; print(certifi.where())"`.
  La descarga completó imprimiendo `(4424, 36)` **sin desactivar la verificación**. Se eliminó
  el `sitecustomize.py` inseguro y se declaró `certifi` en `requirements.txt`. Es un paso de
  entorno (no versionado en código): debe declararse en la terminal antes de lanzar Jupyter.
  Detalle completo en `escritorio_windows10/resolucion_INC-04_detalle.md`.

## 3. Paridad de Resultados

A pesar de los obstáculos, el uso estricto de semillas deterministas (`random_state=42`) y un archivo `requirements.txt` común garantizó la reproducibilidad matemática:

| Métrica (sobre Test) | Arch Linux | Windows 10 | Diferencia |
|---|---|---|---|
| Baseline - AUC-ROC | 0.9301 | 0.9301 | **0.0000** |
| Baseline - Recall Dropout | 0.9000 | 0.9000 | **0.0000** |
| Random Forest - AUC-ROC | 0.9451 | 0.9451 | **0.0000** |
| Random Forest - F1 | 0.8529 | 0.8529 | **0.0000** |

## 4. Conclusión

¿Afectan las diferencias de entorno o los obstáculos a la validez de los resultados? **No.**
La verificación SSL desactivada no alteró *qué* se descargó: el `dataset_audit.csv` del
entorno Windows reportó las mismas **36 variables y 4,424 filas** que la referencia UCI, y
**todas las métricas de test coincidieron exactamente** con las de Arch Linux. Se descargó
el dataset auténtico; el incidente fue de infraestructura de red, no de integridad de datos.

La reproducibilidad multiplataforma quedó demostrada (paridad exacta con Python 3.14 vs 3.13).
El único cabo pendiente —no bloqueante— es sustituir el workaround SSL por el fix versionable
(`certifi`) antes de Fase 3, para que la reproducibilidad en Windows también sea limpia desde
el repositorio.
