# Checklist — corrida del piloto en PC de escritorio (Windows 10)

> Objetivo: ejecutar el mismo piloto de Semana 4 en el entorno Windows 10 para **confirmar
> paridad de resultados** con la laptop (Arch Linux) y **cerrar operativamente INC-01**.
> El procedimiento espeja `../laptop_arch_linux/entorno_ejecucion.md`, adaptado a PowerShell.
>
> **Regla del curso que se mantiene:** HOY SE EJECUTA. Llenar la bitácora *mientras* se
> ejecuta, no al final.

Ejecuta los pasos en orden. Marca cada casilla al completarla.

---

## 0. Prerrequisitos (antes de abrir PowerShell)

- [ ] El repo `Proyecto Github` está sincronizado en esta PC (vía **GitHub Desktop** — el
      `git pull`/`push` lo haces tú manualmente, nunca desde la terminal).
- [ ] Python 3 instalado y en el PATH (`python --version` responde). Idealmente 3.11–3.13;
      si es 3.14 como la laptop, mejor para paridad.
- [ ] Anota el commit de git actual (GitHub Desktop → History, o `git rev-parse --short HEAD`)
      para la tabla de entorno. **Debería ser el mismo commit que corrió la laptop (`a7c0f4f`)**
      o uno posterior que no toque los notebooks.

## 1. Crear el entorno virtual e instalar dependencias

Desde la raíz de `Proyecto Github`, en PowerShell:

```powershell
cd "Proyecto Github"
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
pip freeze > Pilotaje\escritorio_windows10\requirements_lock.txt
```

- [ ] `.venv` creado y activado (el prompt muestra `(.venv)`).
- [ ] `requirements.txt` instalado sin errores.
- [ ] `requirements_lock.txt` generado en `escritorio_windows10/`.

> **Si `Activate.ps1` falla por política de ejecución:**
> `Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned` y reintenta.

> **Paridad estricta (opcional):** si quieres reproducir *exactamente* las mismas versiones
> que la laptop (no solo las que pip resuelva hoy en Windows), instala desde el lock de la
> laptop en vez de `requirements.txt`:
> `pip install -r Pilotaje\laptop_arch_linux\requirements_lock.txt`
> (algún paquete con wheel solo-Linux podría fallar; si pasa, vuelve a `requirements.txt`).

## 1b. Fix del certificado SSL (INC-04) — el correcto, con verificación ACTIVADA

`ucimlrepo` descarga con `urllib.request.urlopen(..., context=ssl.create_default_context(cafile=certifi.where()))`,
es decir **ya usa `certifi` con verificación SSL encendida**. El error `CERTIFICATE_VERIFY_FAILED`
que apareció en la primera corrida fue porque el *bundle* de `certifi` estaba desactualizado.
El fix correcto es actualizarlo (NO desactivar la verificación):

```powershell
pip install --upgrade certifi
```

- [ ] `certifi` actualizado a la última versión.
- [ ] **Eliminar el workaround inseguro** si quedó de la corrida anterior:
      borra `.venv\Lib\site-packages\sitecustomize.py` (desactivaba la verificación SSL de todo
      el entorno). Con `certifi` al día ya no hace falta.

```powershell
Remove-Item ".venv\Lib\site-packages\sitecustomize.py" -ErrorAction SilentlyContinue
```

> Nota: como el contexto fija `cafile=certifi.where()`, la variable `SSL_CERT_FILE` **no tiene
> efecto** aquí — el único lever real es la versión de `certifi`. Si tras actualizar aún
> fallara, la causa sería externa (reloj del sistema desfasado, o un antivirus/proxy que
> intercepta TLS), no el proyecto.

## 2. Pre-vuelo de portabilidad (evita que un notebook reviente a mitad)

- [ ] Verifica que **ningún notebook use el módulo `resource`** (es solo Unix; en Windows
      lanza `ModuleNotFoundError`). En PowerShell, desde la raíz del repo:

```powershell
Select-String -Path "Laboratorio de Pruebas\Fase2_Adquisicion_Preparacion\*.ipynb" -Pattern "import resource|resource\.getrusage"
```

  - Si **no hay coincidencias** → OK, la bitácora usa `psutil` (multiplataforma), sigue.
  - Si **aparece `resource`** en algún notebook → esa celda de medición de memoria fallará
    en Windows. Reemplaza `resource.getrusage(...).ru_maxrss` por la medición con `psutil`
    (`psutil.Process().memory_info().rss`), que ya es la que usa la bitácora. Regístralo
    como incidencia nueva (ver paso 6).
- [ ] `psutil` quedó instalado (está en `requirements.txt`): `python -c "import psutil; print(psutil.__version__)"`.

## 3. Capturar el entorno y completar la ficha

- [ ] Corre la captura de entorno (el mismo criterio que la laptop: `platform`, `psutil`,
      `os.cpu_count`) y **completa la tabla** de `escritorio_windows10/entorno_ejecucion.md`
      (fecha, SO, Python, CPUs, RAM, GPU, commit de git).

## 4. Ejecutar los 4 notebooks EN ORDEN

Ubicación: `Laboratorio de Pruebas\Fase2_Adquisicion_Preparacion\`. Ejecutar **de arriba a
abajo, en este orden**, cada uno completo (Kernel → Restart & Run All):

- [ ] `01_Adquisicion_EDA.ipynb` — adquisición, auditoría, muestra piloto y partición (P01–P05).
- [ ] `02_Piloto_Baseline_RegresionLogistica.ipynb` — baseline (P06).
- [ ] `03_Piloto_RandomForest.ipynb` — 2º modelo sin tuning.
- [ ] `04_Piloto_Evaluacion_Test.ipynb` — evaluación única sobre test (P10).

> Las salidas deben escribirse en `Pilotaje\escritorio_windows10\evidencias\` (no en las de
> la laptop ni en `data\processed\`). Verifica que la ruta de salida de los notebooks apunte
> a este entorno; si los notebooks fijan la ruta de la laptop, ajústala o copia las
> evidencias generadas a la carpeta de Windows al terminar.

- [ ] Los 4 notebooks corrieron con **0 errores**.

## 5. Verificar PARIDAD de resultados vs. la laptop

Compara las métricas clave contra las de la laptop (`../laptop_arch_linux/evidencias/`):

| Métrica (test) | Laptop (Arch Linux) | Windows 10 | ¿Coincide? |
|---|---|---|---|
| Baseline — AUC-ROC | 0.9301 | | |
| Baseline — F1 | 0.8630 | | |
| Baseline — Recall Dropout | 0.9000 | | |
| Random Forest — AUC-ROC | 0.9451 | | |
| Random Forest — F1 | 0.8529 | | |

- [ ] Con `random_state=42` fijo, los valores deberían coincidir **a 3–4 decimales**.
      Diferencias en el último decimal son normales (BLAS/orden de operaciones por SO).
- [ ] Si alguna métrica difiere de forma notable (> ~0.01), **no cierres INC-01**: registra
      la diferencia como incidencia y revisa versiones de librerías (`requirements_lock.txt`
      de ambos entornos) antes de concluir.

## 6. Llenar la bitácora y registrar incidencias nuevas

- [ ] Completa `escritorio_windows10\bitacora_ejecucion.csv` con los pasos ejecutados
      (mismo formato de columnas que la laptop: `id,accion,entrada,esperado,observado,incidencia,evidencia`).
- [ ] Si surge algo nuevo (p. ej. el módulo `resource`, una ruta hardcodeada, una versión de
      librería distinta), agrégalo a `..\incidencias_log.csv` como `INC-04`, `INC-05`…

## 7. Cerrar INC-01

- [ ] Si la paridad se confirma: anota en `..\incidencias_log.csv` (fila INC-01) que la
      ejecución en Windows 10 **confirmó paridad** y que el §10 del Protocolo **v2.2** ya
      declara ambos entornos → INC-01 **cerrada**.
- [ ] Añade una nota breve en el `Informe_Pilotaje_V1.md` (sección E, INC-01) indicando la
      fecha de la corrida en Windows y el resultado de la comparación.

---

**Resultado esperado:** piloto reproducido en los dos entornos declarados por el Protocolo
v2.2 §10, con paridad de métricas documentada → INC-01 cerrada y reproducibilidad
multiplataforma demostrada (punto fuerte para la exposición).
