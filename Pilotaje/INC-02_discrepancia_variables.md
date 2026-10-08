# INC-02 — Discrepancia: 36 variables predictoras, no 35

**Estado:** Pendiente de decisión. No se modifica el Protocolo v2.1 todavía — se prioriza
el piloto de Semana 4. Documentado aquí para no perder el hallazgo ni la investigación
que lo respalda.

**Detectado en:** ejecución del piloto, 2026-10-04, entorno `laptop_arch_linux` —
`dataset_audit.csv` (sección 6 del notebook `01_Adquisicion_EDA.ipynb`).

**Registrado en:** `Pilotaje/incidencias_log.csv`, fila `INC-02`.

## El hallazgo

El Protocolo de Investigación v2.1, §5 ("Dataset — ficha técnica"), documenta:

> Registros originales: 4,424 · **35 variables predictoras**

Pero `dataset_audit.csv`, generado al ejecutar el piloto sobre el dataset realmente
descargado (UCI id=697, vía `ucimlrepo`), reporta:

```
n_registros_totales: 4424
n_variables_predictoras: 36
discrepancia_variables: True
```

Verificado además de forma independiente, sin depender de una sola fuente:

1. **CSV crudo descargado** (`data/raw/dataset_crudo_uci697.csv`): 37 columnas totales.
   36 son predictoras + 1 es `Target`. Confirmado contando `df.columns` directamente.
2. **Metadatos de `ucimlrepo` en Python**: `dataset.metadata.num_features == 36`.
3. **Página oficial del UCI Machine Learning Repository**
   (`archive.ics.uci.edu/dataset/697/`): declara explícitamente `# Features == 36`,
   con una tabla de variables de "37 filas" (36 features + target).

## El origen de la discrepancia

El protocolo citó correctamente el paper original:

> Realinho, V., Machado, J., Baptista, L., & Martins, M. V. (2022). *Predicting Student
> Dropout and Academic Success*. Data, 7(11), 146.

El abstract de ese paper (verificado vía EconPapers/MDPI) dice explícitamente:
*"The dataset contains 4424 records with **35 attributes**..."*

Es decir: **el paper citable describe 35 atributos, pero el dataset que efectivamente
se sirve en el repositorio UCI (de donde se descarga el dato real, vía `ucimlrepo`
id=697) trae 36.** No es un error del protocolo al citar la fuente — es una divergencia
real entre la publicación académica y la versión del dataset actualmente alojada en el
repositorio. Un tercer indicio de que es una discrepancia conocida (no un error de
nuestra descarga): varias fuentes secundarias (p. ej. descripciones de datasets
derivados en Kaggle) señalan explícitamente que "algunas fuentes citan 35 atributos,
otras 36", consistente con que el repositorio UCI fue actualizado después de la
publicación del paper.

## Candidato a variable añadida (no confirmado con certeza)

La variable `International` (flag binario `1=sí / 0=no`, categoría demográfica) es la
candidata más plausible a ser la variable agregada después del paper original:

- Es conceptualmente redundante con `Nacionality` (ya existe esa variable en las 35
  originales).
- Aparece al final del bloque de variables demográficas en el orden de columnas, un
  patrón típico de columna añadida en una revisión posterior del dataset.

**Esto NO está confirmado al 100%.** No fue posible extraer de forma confiable la tabla
de variables del PDF del paper original (contenido binario/comprimido, no se dejó leer
como texto) para hacer un diff exacto 1:1 contra las 36 variables actuales. Es la
hipótesis más razonable con la evidencia disponible, no un hecho verificado.

## Por qué no se excluye la variable

No hay ningún motivo técnico para tratarla como un error de datos:

- No tiene valores faltantes.
- No es una columna duplicada ni un identificador filtrado.
- Es una variable predictora legítima, servida oficialmente por el UCI, con la misma
  calidad que las otras 35.

## Recomendación (no aplicada todavía)

Cuando se retome esta decisión, actualizar el Protocolo v2.1 §5 de "35 variables
predictoras" a **36**, citando como fuente los metadatos de UCI id=697 al momento de la
descarga (no solo el paper), y dejar una nota explícita de que el paper original
describe 35. Esto, siguiendo la propia regla de versionado del protocolo (§11, P15),
correspondería a un incremento de versión (v2.1 → v2.2) — no un cambio silencioso.

## Impacto mientras quede pendiente

Ninguno sobre la ejecución del piloto ni sobre OE1/OE2: el notebook y el pipeline del
piloto ya usan las 36 variables reales servidas por UCI (confirmable en
`pipeline_config_piloto.json`, campo `variables_usadas`), independientemente de qué
número declare el §5 del protocolo. El desajuste es únicamente entre lo documentado en
el protocolo y la realidad del dataset — no afecta la validez de los resultados
obtenidos hasta ahora.
