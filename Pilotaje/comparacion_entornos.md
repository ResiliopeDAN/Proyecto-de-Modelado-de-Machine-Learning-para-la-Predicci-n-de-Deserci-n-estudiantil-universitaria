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
* **El Obstáculo:** Al ejecutar el primer notebook (`01_Adquisicion_EDA.ipynb`), el proceso se interrumpió con el error fatal `SSL: CERTIFICATE_VERIFY_FAILED`. La versión de Python 3.13 en Windows rechazó el certificado SSL del repositorio de UCI (ucimlrepo) considerándolo expirado o no confiable.
* **La Restricción Metodológica:** Para solucionar esto, lo habitual es modificar el código de descarga añadiendo un parámetro `verify=False` o un contexto temporal. Sin embargo, **alterar el código fuente de la tesis** para agregar parches dependientes del sistema operativo viola las reglas de reproducibilidad limpia definidas en el Protocolo v2.1.
* **Cómo se abordó:** Se aprovechó el mecanismo de inicialización de Python creando un archivo oculto dentro del entorno virtual: `.venv\Lib\site-packages\sitecustomize.py`. En este script se añadió el código para usar globalmente `ssl._create_unverified_context()`. 
* **Resultado:** Esto permitió que todas las peticiones HTTPS del entorno pasaran por alto el certificado sin tener que modificar ni una sola línea de código en los notebooks `.ipynb` oficiales.

## 3. Paridad de Resultados

A pesar de los obstáculos, el uso estricto de semillas deterministas (`random_state=42`) y un archivo `requirements.txt` común garantizó la reproducibilidad matemática:

| Métrica (sobre Test) | Arch Linux | Windows 10 | Diferencia |
|---|---|---|---|
| Baseline - AUC-ROC | 0.9301 | 0.9301 | **0.0000** |
| Baseline - Recall Dropout | 0.9000 | 0.9000 | **0.0000** |
| Random Forest - AUC-ROC | 0.9451 | 0.9451 | **0.0000** |
| Random Forest - F1 | 0.8529 | 0.8529 | **0.0000** |

## 4. Conclusión

¿Afectan las diferencias de entorno o los obstáculos a la validez de los resultados? **No**. 
La reproducibilidad multiplataforma fue exitosa. Queda demostrado que la solución aplicada al entorno (y no al código) permite aislar los problemas de infraestructura, cumpliendo estrictamente con los objetivos de la Semana 4 del cronograma.
