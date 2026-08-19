# Caso_de_Estudio_HIGGS

Detección y priorización de eventos anómalos en el dataset HIGGS mediante enfoques supervisado, no supervisado e integrado..

## Notebook principal

El notebook que consolida los Requerimientos 1 a 6 (EDA, enfoque supervisado, enfoque no supervisado, priorización de alertas, integración y preguntas de análisis) es:

```
notebooks/Requerimientos_1_y_2_y_3.ipynb
```

## 1. Datos

Los archivos de datos **no están incluidos en el repositorio** (excluidos vía `.gitignore` por su tamaño). Antes de ejecutar el notebook, coloca los dos archivos del dataset en la carpeta `data/`:

```
data/higgs_anomaly_400k_part1.parquet
data/higgs_anomaly_400k_part2.parquet
```

El notebook los carga y concatena automáticamente en la primera celda (≈400,000 filas, 30 columnas).

## 2. Entorno y dependencias

Se probó con **Python 3.14** y las siguientes versiones de librerías (versiones más recientes de la misma rama mayor también deberían funcionar):

| Paquete | Versión probada |
|---|---|
| pandas | 3.0.2 |
| numpy | 2.4.4 |
| scikit-learn | 1.8.0 |
| imbalanced-learn | 0.14.2 |
| matplotlib | 3.10.8 |
| seaborn | 0.13.2 |
| joblib | 1.5.3 |
| pyarrow | 23.0.1 |
| jupyter / jupyterlab / ipykernel | cualquier versión reciente |

### Crear el entorno con conda

```bash
conda create -n higgs python=3.14 -y
conda activate higgs
pip install pandas numpy scikit-learn imbalanced-learn matplotlib seaborn joblib pyarrow jupyterlab ipykernel
```

### O con venv + pip

```bash
python -m venv .venv
.venv\Scripts\activate          # Windows
pip install pandas numpy scikit-learn imbalanced-learn matplotlib seaborn joblib pyarrow jupyterlab ipykernel
```

## 3. Cómo ejecutar el notebook

1. Abre el proyecto en VS Code (con la extensión Jupyter) o lanza Jupyter Lab **desde la carpeta `notebooks/`**:
   ```bash
   cd notebooks
   jupyter lab
   ```
   Esto es importante porque el notebook usa rutas relativas (`../data/...`, `modelos/...`, `../models/...`); si se ejecuta con otro directorio de trabajo, las celdas de carga de datos y de guardado de modelos fallarán.
2. Selecciona el entorno creado en el paso 2 como kernel del notebook.
3. Ejecuta todas las celdas **en orden, de principio a fin** (`Run All` / `Restart Kernel and Run All`). El notebook depende de estado acumulado entre secciones (particiones de datos, escaladores, modelos entrenados), por lo que no se puede ejecutar una sección salteando las anteriores.
4. Tiempo estimado: la ejecución completa toma entre **10 y 20 minutos**, dominado por tres pasos: la validación cruzada de 5 pliegues y la importancia por permutación del modelo supervisado (Sección 2.2), y el ajuste de Local Outlier Factor sobre el enfoque no supervisado (Sección 3), que por su costo computacional se entrena sobre una submuestra de 80,000 observaciones.

## 4. Artefactos generados

Al ejecutar el notebook completo se generan (rutas relativas a `notebooks/`):

- `modelos/Modelo_enfoque_supervisado.joblib` — pipeline del modelo supervisado final (con escalador incluido si aplica), ambos umbrales calibrados (máximo F1 y presupuesto de alertas) y metadatos de la partición.
- `../models/scaler_no_supervisado.joblib` — escalador usado en el enfoque no supervisado.
- `../models/isolation_forest_{bajo_nivel,alto_nivel,completo}.joblib` — modelos Isolation Forest por bloque de variables.
- `../models/lof_{bajo_nivel,alto_nivel,completo}.joblib` — modelos Local Outlier Factor por bloque de variables.

Estas carpetas (`modelos/` y `models/`) se crean automáticamente si no existen; no requieren configuración previa.

