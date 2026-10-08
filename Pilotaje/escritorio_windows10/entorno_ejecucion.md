# Entorno de ejecución — PC de escritorio (Windows 10)

Capturado automáticamente antes de ejecutar el piloto, para que cualquier persona sin
conocimiento previo del proyecto pueda reproducir exactamente este entorno.

| Campo | Valor |
|---|---|
| Fecha/hora de captura | 2026-10-08T12:18:29 (hora local) |
| Sistema operativo | Windows-10-10.0.19045-SP0 |
| Python | 3.13.1 (CPython) |
| CPUs lógicas | 16 |
| RAM total | 31.9 GB |
| RAM disponible al momento de la captura | 20.7 GB |
| GPU | No detectada / no requerida (Protocolo v2.1 §10: GPU no requerida) |
| Commit de git al iniciar el piloto | Mismo que laptop (`a7c0f4f`) — verificable desde GitHub Desktop (git no está en PATH) |

## Cómo se construyó el entorno

```powershell
python -m venv .venv
.venv\Scripts\python.exe -m pip install --upgrade pip
.venv\Scripts\python.exe -m pip install -r requirements.txt
.venv\Scripts\python.exe -m pip freeze > Pilotaje\escritorio_windows10\requirements_lock.txt
```

`requirements.txt` (raíz del repo) es la lista declarativa de dependencias, igual para
cualquier entorno. `requirements_lock.txt` (en esta carpeta) es el `pip freeze` exacto
de **esta máquina en este momento** — las versiones realmente resueltas por pip, no un
rango. Si se reproduce en otra máquina con el mismo `requirements.txt` pero en otro
momento, pip podría resolver versiones distintas; para reproducir exactamente este
piloto, instalar desde `requirements_lock.txt`:

```powershell
.venv\Scripts\python.exe -m pip install -r Pilotaje\escritorio_windows10\requirements_lock.txt
```

## Diferencias con el entorno de la laptop (Arch Linux)

| Componente | Laptop (Arch Linux) | Escritorio (Windows 10) |
|---|---|---|
| Python | 3.14.7 | 3.13.1 |
| SO | Linux-7.1.11-arch1-1 | Windows-10-10.0.19045-SP0 |
| CPUs | 12 | 16 |
| RAM | 31.0 GB | 31.9 GB |

La diferencia de versión de Python (3.14 vs 3.13) no debería afectar la paridad de
resultados dado que las librerías de cálculo (`numpy`, `scikit-learn`, `scipy`) tienen
las mismas versiones en ambos entornos y `random_state=42` está fijado en cada paso.

## Medición de recursos durante la ejecución

Cada paso pesado del notebook registra tiempo de ejecución y uso de memoria pico en la
bitácora (`bitacora_ejecucion.csv`), usando `time.perf_counter()` y `psutil` dentro
del propio notebook — multiplataforma, funcional en Windows sin necesidad de `resource`.

No instalar nada a nivel de sistema: usar siempre el `.venv` del proyecto.
