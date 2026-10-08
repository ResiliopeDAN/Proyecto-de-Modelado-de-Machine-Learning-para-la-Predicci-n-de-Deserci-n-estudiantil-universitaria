# Pilotaje — Semana 4 (Seminario de Tesis II)

Ejecución real y reducida del Protocolo v2.1 (pasos P01–P06: adquisición → auditoría →
control de fuga → partición → preprocesamiento → entrenamiento), con evidencia
verificable, siguiendo el "Laboratorio Semana 4 — Ejecución del Piloto y
Retroalimentación" (Dra. Huancapaza) y las slides `Sem4_TesisII_UNAJ.pdf`
(`02_Apuntes/Pilotaje/`).

El entrenamiento es **modular**: cada modelo vive en su propio notebook
(`Laboratorio de Pruebas/Fase2_Adquisicion_Preparacion/`), reutilizando la misma
muestra/partición piloto generada una sola vez:

1. `01_Adquisicion_EDA.ipynb` — P01-P05: adquisición, EDA, auditoría, muestra piloto,
   partición, pipeline (escalado + SMOTE). Genera la evidencia compartida.
2. `02_Piloto_Baseline_RegresionLogistica.ipynb` — P06, el baseline del Protocolo v2.1.
3. `03_Piloto_RandomForest.ipynb` — un segundo modelo (Random Forest, **sin tuning**),
   corrida adicional no oficial, solo para confirmar que el pipeline es agnóstico al
   algoritmo. No reemplaza ni adelanta el tuning con Optuna de Fase 4 (semana 8).
4. `04_Piloto_Evaluacion_Test.ipynb` — reentrana ambos modelos y evalúa **una única vez**
   sobre el test piloto (Protocolo v2.1 §11 P10, regla de uso único del test), genera
   matriz de confusión por modelo. Cierra el ciclo train→val→test del piloto.

## Por qué dos entornos

El autor dispone de dos máquinas: una laptop (Arch Linux) y una PC de escritorio
(Windows 10). El Protocolo v2.1 §10 declaraba Windows 10 como entorno de cómputo; al
preparar este piloto se detectó que el trabajo real también se hace en la laptop Linux.
En vez de forzar un solo entorno "oficial", el piloto corre en **ambos**, documentando
cada uno por separado — esto es, en sí mismo, una prueba de la dimensión "Factibilidad"
y "Tiempo/recursos" que pide la Semana 4, y deja evidencia de que el pipeline es
reproducible entre sistemas operativos distintos, no solo en una máquina particular.

## Estructura

```
Pilotaje/
├── README.md                      Este archivo
├── laptop_arch_linux/              Ejecución en la laptop (Arch Linux)
│   ├── entorno_ejecucion.md        SO, kernel, Python, CPU/RAM, cómo se creó el venv
│   ├── requirements_lock.txt       pip freeze exacto de esta máquina (versiones fijas)
│   ├── bitacora_ejecucion.csv      Bitácora P-01..P-06: acción, entrada, esperado, observado, incidencia, evidencia
│   ├── logs/                       Logs crudos de ejecución (stdout, tiempos)
│   └── evidencias/                 dataset_metadata.csv, dataset_audit.csv, dataset_split_piloto.csv,
│                                    pipeline_config_piloto.json, comparacion_test_piloto.csv,
│                                    figuras/ (compartidos entre modelos)
│                                    ├── baseline_regresion_logistica/   config + métricas val/test + matriz confusión
│                                    └── random_forest/                 config + métricas val/test + matriz confusión
├── escritorio_windows10/           Misma estructura, para la PC de escritorio (Windows 10)
├── incidencias_log.csv             Incidencias técnicas/metodológicas detectadas en cualquiera de los dos entornos
└── comparacion_entornos.md         Se completa cuando ambos entornos ya corrieron: diferencias de
                                     versiones, tiempos, resultados — y si afectan la validez
```

## Regla de trazabilidad

Cada archivo en `evidencias/` debe poder reconstruirse a partir de: la versión del
Protocolo (`v2.1`), el `requirements_lock.txt` de ese entorno, y el notebook versionado
en `../Laboratorio de Pruebas/Fase2_Adquisicion_Preparacion/`. El código del pipeline
vive en un solo lugar (`Laboratorio de Pruebas/`); lo que es específico de cada máquina
son el entorno y las evidencias que produce, no una copia del notebook.

## Registro de cambios e incidencias

- `registro_cambios_preparacion.md` — todo lo que se modificó en el repo para preparar el
  piloto (notebook, índice, README, entorno), clasificado técnico/metodológico.
- `incidencias_log.csv` — hallazgos durante la ejecución real: INC-01 (entorno Windows vs.
  Linux, pendiente), INC-02 (el dataset trae 36 variables predictoras, no 35 como documenta
  el Protocolo v2.1 §5 — pendiente de decidir), INC-03 (Arch Linux obliga a usar venv, resuelto).

## Estado

| Entorno | Estado | Fecha de ejecución |
|---|---|---|
| Laptop (Arch Linux) | ✅ Ejecutado — P01-P06 sin errores, evidencia en `laptop_arch_linux/evidencias/` | 2026-10-04 |
| Escritorio (Windows 10) | Pendiente | — |
