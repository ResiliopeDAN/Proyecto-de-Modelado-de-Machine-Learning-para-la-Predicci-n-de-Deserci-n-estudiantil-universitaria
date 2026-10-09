# Reporte Técnico: Resolución Definitiva de INC-04 (Error SSL)

## Contexto de la Intervención
Durante la ejecución del pilotaje en Windows 10, se detectó el error `SSL: CERTIFICATE_VERIFY_FAILED` al intentar descargar el dataset mediante `ucimlrepo`. 
Inicialmente se introdujo un parche inseguro (`.venv\Lib\site-packages\sitecustomize.py`) para desactivar globalmente la validación SSL. El objetivo de esta intervención fue eliminar dicho parche, purificar el entorno y hacer funcionar la descarga con la validación SSL estrictamente activada, asumiendo que actualizar el paquete `certifi` sería suficiente.

## Procedimiento y Hallazgos

### 1. Eliminación del parche y actualización
Se ejecutaron los comandos para actualizar los certificados y borrar el hack:
```powershell
pip install --upgrade certifi
Remove-Item ".venv\Lib\site-packages\sitecustomize.py" -ErrorAction SilentlyContinue
```

### 2. Fallo en la comprobación y causa raíz
Al ejecutar la prueba de descarga (`python -c "from ucimlrepo import fetch_ucirepo..."`), el comando **volvió a fallar** arrojando el mismo error de validación SSL.

**Análisis de la causa raíz:**
Se descubrió un comportamiento mixto en el código fuente de `ucimlrepo`. El diagnóstico inicial de que la librería utilizaba internamente `certifi` (`context=ssl.create_default_context(cafile=certifi.where())`) era correcto, pero **solo aplicaba a la solicitud de metadata (el archivo JSON)**. 
Para descargar el archivo de datos real (CSV), `ucimlrepo` delega la tarea a `pandas.read_csv()`. Pandas no utiliza `certifi` de manera predeterminada; recae en la librería estándar `urllib`, la cual utiliza el almacén de certificados nativo del sistema operativo (Windows 10). Al estar desactualizado el almacén de Windows, Pandas rechazaba la conexión.

### 3. Solución Implementada (Sin alterar los notebooks)
Para cumplir con la regla de oro del proyecto (no modificar el código de los archivos `.ipynb` ni ensuciar el entorno `.venv`), la solución correcta fue configurar el entorno del terminal (PowerShell) para que OpenSSL utilizara los certificados actualizados de `certifi` en toda la sesión:

```powershell
$env:SSL_CERT_FILE = .venv\Scripts\python.exe -c "import certifi; print(certifi.where())"
.venv\Scripts\python.exe -c "from ucimlrepo import fetch_ucirepo; d=fetch_ucirepo(id=697); print(d.data.features.shape)"
```

**Resultado:** Tras inyectar la variable de entorno, `pandas.read_csv()` heredó correctamente el contexto seguro y la prueba finalizó de manera exitosa imprimiendo la dimensionalidad correcta: `(4424, 36)`.

## Impacto Metodológico
1. **Entorno puro:** El `.venv` fue devuelto a su estado natural, libre de manipulaciones internas.
2. **Dependencias:** Se regeneró el archivo `requirements_lock.txt` para reflejar el estado actual.
3. **Reproducibilidad:** Si en el futuro se ejecuta este pipeline en un Windows con certificados raíz caducados, el usuario deberá declarar `$env:SSL_CERT_FILE` en su terminal antes de iniciar Jupyter. Esto delega la responsabilidad a la configuración del sistema, preservando la integridad del código fuente de la tesis.
