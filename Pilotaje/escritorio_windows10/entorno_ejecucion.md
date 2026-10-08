# Entorno de ejecución — PC de escritorio (Windows 10)

Pendiente de completar. Mismo procedimiento que `../laptop_arch_linux/entorno_ejecucion.md`,
adaptado a PowerShell:

```powershell
cd "Proyecto Github"
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
pip freeze > Pilotaje\escritorio_windows10\requirements_lock.txt
```

Luego ejecutar en esta máquina el mismo script de captura de entorno usado en la laptop
(`platform`, `psutil`, `os.cpu_count`) y completar esta tabla:

| Campo | Valor |
|---|---|
| Fecha/hora de captura | |
| Sistema operativo | |
| Python | |
| CPUs lógicas | |
| RAM total | |
| RAM disponible al momento de la captura | |
| GPU | |
| Commit de git al iniciar el piloto | |

No instalar nada a nivel de sistema: usar siempre el `.venv` del proyecto.
