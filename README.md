# Proyecto IA 23-4 (Modular) - Guia tecnica 0 a 100

## 1) Marco conceptual solicitado

### Backend frame

- `EDA - preprocess` como capa base.
- Herencia por responsabilidad comun:
- `No supervisado -> cluster` y reduccion dimensional.
- `Supervisado -> clasificacion - regresion`, con logica compartida en la base supervisada.

### Front

- `Streamlit` como interfaz.
- Trabajo por capas.
- Patron `MVC`.

## 2) Arquitectura aplicada en el codigo

### Backend por herencia

- `Backend/EDA/eda.py`
- Clase base `EDAEngine`.
- Responsabilidad: carga de datos, limpieza, conversion a matriz numerica, encoding, escalado, cache y split.

- `Backend/Supervisados/supervisado_base.py`
- Hereda de `EDAEngine`.
- Responsabilidad: preparar datos supervisados y flujo comun de entrenamiento/prediccion.

- `Backend/Supervisados/Clasificacion.py`
- Hereda de `SupervisadoBase`.
- Responsabilidad: algoritmos de clasificacion.

- `Backend/Supervisados/Regresion.py`
- Hereda de `SupervisadoBase`.
- Responsabilidad: algoritmos de regresion.

- `Backend/No_supervisados/no_supervisado_base.py`
- Hereda de `EDAEngine`.
- Responsabilidad: matriz base no supervisada y evaluacion comun de clustering.

- `Backend/No_supervisados/Cluster.py`
- Hereda de `NoSupervisadoBase`.
- Responsabilidad: KMeans, MiniBatchKMeans, KMedoids (si aplica), HAC, DBSCAN.

- `Backend/No_supervisados/reduccion.py`
- Hereda de `NoSupervisadoBase`.
- Responsabilidad: PCA, t-SNE, UMAP.

- `Backend/Pipelines/experimentos.py`
- Orquestador `MotorExperimentos`.
- Responsabilidad: ejecucion de benchmarks y coordinacion de modulos.

- `Backend/Metricas/*`
- Responsabilidad: metricas supervisadas y metricas de clustering.

### Frontend en capas MVC

- `View`:
- `Frontend/streamlit_app.py`
- UI, carga de CSV, seleccion de columnas, seleccion de target, botones de ejecucion y visualizacion de resultados.

- `Controller`:
- `Frontend/controller.py`
- Puente entre interfaz y backend.

- `Model`:
- `Backend/Pipelines/experimentos.py` + clases de dominio en `Backend/*`.
- Logica de negocio y entrenamiento.

## 3) Flujo tecnico de ejecucion

1. Carga de archivo CSV en Streamlit.
2. Deteccion de separador (`;` o `,`).
3. Eliminacion opcional de columnas desde `Columnas a eliminar antes de entrenar`.
4. Seleccion de variable objetivo para tareas supervisadas.
5. Llamada del controlador al `MotorExperimentos`.
6. Preparacion de datos en `EDAEngine`.
7. Entrenamiento de modelos segun tarea.
8. Calculo y retorno de metricas.
9. Render de resultados en interfaz.

## 4) Interfaz actual (en español)

Elementos principales de la vista:

- `Preprocesamiento rápido`
- `Columnas a eliminar antes de entrenar` (selector multiple)
- `Variable objetivo (solo supervisado)` con placeholder `Seleccionar variable objetivo`
- `Probar modelos de clasificación`
- `Probar modelos de regresión`
- `Probar modelos de agrupamiento`

Panel informativo:

- `Modelos incluidos en cada boton`
- Clasificación: `KNN`, `Decision Tree`, `Random Forest`, `Gradient Boosting`, `AdaBoost`
- Regresión: `Lineal`, `LassoCV`, `RidgeCV`, `SVR`, `Árbol`, `Bosque`, `Boosting`
- Agrupamiento: `KMeans`, `MiniBatchKMeans`, `HAC` y `KMedoids` cuando está disponible
## 5) Estructura del proyecto

```text
Proyecto/
├── Backend/
│   ├── EDA/eda.py
│   ├── Supervisados/
│   │   ├── supervisado_base.py
│   │   ├── Clasificacion.py
│   │   └── Regresion.py
│   ├── No_supervisados/
│   │   ├── no_supervisado_base.py
│   │   ├── Cluster.py
│   │   └── reduccion.py
│   ├── Metricas/
│   │   ├── metricas_supervisado.py
│   │   └── metricas_cluster.py
│   ├── Visualizacion/
│   │   ├── cluster_plots.py
│   │   └── pca_plots.py
│   └── Pipelines/experimentos.py
├── Frontend/
│   ├── controller.py
│   └── streamlit_app.py
├── main.py
└── README.md
```

## 6) Instalacion desde cero

Ruta base de ejemplo:

- macOS/Linux: `/Users/billitas/Documents/proyectos/IA 23-4`
- Windows: `C:\Users\USUARIO\Documents\proyectos\IA 23-4`

### macOS/Linux

```bash
cd "/Users/billitas/Documents/proyectos/IA 23-4"
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install streamlit pandas numpy scikit-learn matplotlib seaborn scikit-learn-extra umap-learn
cd Proyecto
```

### Windows PowerShell

```powershell
$ROOT = "$env:USERPROFILE\Documents\proyectos\IA 23-4"
Set-Location $ROOT
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install streamlit pandas numpy scikit-learn matplotlib seaborn scikit-learn-extra umap-learn
Set-Location .\Proyecto
```

Si existe bloqueo de scripts en PowerShell:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\.venv\Scripts\Activate.ps1
```

### Windows CMD

```cmd
cd /d "%USERPROFILE%\Documents\proyectos\IA 23-4"
python -m venv .venv
call .venv\Scripts\activate.bat
python -m pip install --upgrade pip
python -m pip install streamlit pandas numpy scikit-learn matplotlib seaborn scikit-learn-extra umap-learn
cd Proyecto
```

## 7) Compilar y verificar (0 a 100)

En Python, compilar equivale a validar sintaxis y generar bytecode (`.pyc`).

### macOS/Linux

```bash
cd "/Users/billitas/Documents/proyectos/IA 23-4/Proyecto"
source ../.venv/bin/activate
python -m compileall Backend Frontend main.py
python -m py_compile Frontend/streamlit_app.py Backend/Pipelines/experimentos.py Backend/EDA/eda.py
```

### Windows PowerShell

```powershell
Set-Location "$env:USERPROFILE\Documents\proyectos\IA 23-4\Proyecto"
& "$env:USERPROFILE\Documents\proyectos\IA 23-4\.venv\Scripts\Activate.ps1"
python -m compileall Backend Frontend main.py
python -m py_compile Frontend/streamlit_app.py Backend/Pipelines/experimentos.py Backend/EDA/eda.py
```

### Windows CMD

```cmd
cd /d "%USERPROFILE%\Documents\proyectos\IA 23-4\Proyecto"
call ..\.venv\Scripts\activate.bat
python -m compileall Backend Frontend main.py
python -m py_compile Frontend/streamlit_app.py Backend/Pipelines/experimentos.py Backend/EDA/eda.py
```

## 8) Ejecucion

### macOS/Linux

```bash
cd "/Users/billitas/Documents/proyectos/IA 23-4/Proyecto"
source ../.venv/bin/activate
streamlit run Frontend/streamlit_app.py
```

### Windows PowerShell

```powershell
Set-Location "$env:USERPROFILE\Documents\proyectos\IA 23-4\Proyecto"
& "$env:USERPROFILE\Documents\proyectos\IA 23-4\.venv\Scripts\Activate.ps1"
streamlit run Frontend/streamlit_app.py
```

### Windows CMD

```cmd
cd /d "%USERPROFILE%\Documents\proyectos\IA 23-4\Proyecto"
call ..\.venv\Scripts\activate.bat
streamlit run Frontend/streamlit_app.py
```

URL local por defecto:

- `http://localhost:8501`

## 9) Glosario de comandos

| Comando | Uso |
|---|---|
| `python -m venv .venv` | Crear entorno virtual |
| `source .venv/bin/activate` | Activar entorno en macOS/Linux |
| `.\.venv\Scripts\Activate.ps1` | Activar entorno en PowerShell |
| `call .venv\Scripts\activate.bat` | Activar entorno en CMD |
| `python -m pip install ...` | Instalar dependencias |
| `python -m compileall Backend Frontend main.py` | Compilar bytecode por carpetas |
| `python -m py_compile archivo.py` | Verificar sintaxis de archivo puntual |
| `streamlit run Frontend/streamlit_app.py` | Iniciar app web |
| `streamlit cache clear` | Limpiar cache de Streamlit |
| `deactivate` | Salir del entorno virtual |

## 10) Problemas comunes

### Error de desconexion (`DoInitPings` / no response)

1. Verificacion de proceso Streamlit activo en terminal.
2. Reinicio de servidor:

```bash
streamlit run Frontend/streamlit_app.py
```

3. Recarga forzada del navegador (`Cmd+Shift+R` o `Ctrl+F5`).

### Error `No module named Frontend` o `No module named Backend`

- Ejecucion desde carpeta `Proyecto`.
- Entorno virtual activo.

### `KMedoids` ausente en benchmark de agrupamiento

- Comportamiento esperado cuando `scikit-learn-extra` no esta disponible o no es compatible con la version de `numpy`.
- Continuidad operativa con KMeans, MiniBatchKMeans y HAC.

## 11) Datasets de prueba incluidos

Carpeta:

- `Proyecto/Datasets`

Archivos por carpeta:

- `Datasets/clasificacion/01_clasificacion_clientes.csv`
- Target recomendado: `churn`
- Prueba ideal: clasificación (también permite agrupamiento)

- `Datasets/regresion/02_regresion_viviendas.csv`
- Target recomendado: `precio`
- Prueba ideal: regresión (también permite agrupamiento)

- `Datasets/agrupamiento/03_agrupamiento_clientes.csv`
- Target: no aplica
- Prueba ideal: agrupamiento

- `Datasets/mixto/04_mixto_todo_semicolon.csv`
- Separador: `;`
- Target clasificación: `riesgo_fuga`
- Target regresión: `valor_vida`
- Prueba ideal: clasificación, regresión y agrupamiento

Metadatos:

- `Datasets/META_DATASETS.md`
