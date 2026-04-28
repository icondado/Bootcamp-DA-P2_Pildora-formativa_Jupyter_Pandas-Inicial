# 🐼 Jupyter: Pandas Inicial
## Carga de datos, gestión de valores Nulos y Duplicados

> 📚 **Píldora de estudio** — Introducción práctica a la limpieza de datos con Python, Pandas y Jupyter Notebooks.


## 📋 Índice

[Paso 1 — Carga de Datos](#paso-1--carga-de-datos)

[Paso 2 — Identificar Valores Nulos](#paso-2--identificar-valores-nulos-nan)

[Paso 3 — Manejar Valores Nulos](#paso-3--manejar-valores-nulos)

[Paso 4 — Identificar Duplicados](#paso-4--identificar-filas-duplicadas)

[Paso 5 — Eliminar Duplicados](#paso-5--eliminar-filas-duplicadas)

[Flujo Completo de Limpieza](#flujo-completo-de-limpieza)

[Buenas Prácticas](#buenas-prácticas)

[Cheatsheet — Referencia Rápida](#cheatsheet--referencia-rápida)

[Recursos](#recursos)

[🎮 ¡Pon a prueba tus conocimientos!](#-pon-a-prueba-tus-conocimientos)


## Paso 1 — Carga de Datos

Pandas ofrece funciones versátiles para importar datos desde múltiples formatos.


### 📂 Funciones de carga

| Función | Formato | Descripción |
|---|---|---|
| `pd.read_csv('archivo.csv')` | CSV | Lee un archivo de texto con valores separados por comas |
| `pd.read_excel('archivo.xlsx')` | Excel | Lee libros de cálculo de Microsoft Excel |
| `pd.read_json('archivo.json')` | JSON | Lee datos estructurados en formato JavaScript |
| `pd.read_sql(query, conn)` | SQL | Lee datos directamente desde una base de datos |

### 🔍 Funciones de exploración inicial

| Función | Descripción |
|---|---|
| `df.head(n)` | Muestra las primeras `n` filas (por defecto 5). Útil para verificar que la carga fue correcta |
| `df.tail(n)` | Muestra las últimas `n` filas del DataFrame |
| `df.shape` | Devuelve una tupla `(filas, columnas)` con las dimensiones del DataFrame |
| `df.info()` | Muestra un resumen de tipos de datos, columnas y valores no nulos |
| `df.describe()` | Genera estadísticos descriptivos (media, desviación, mínimo, máximo…) de columnas numéricas |
| `df.dtypes` | Muestra el tipo de dato de cada columna |
| `df.columns` | Lista los nombres de todas las columnas |
| `df.copy()` | Crea una copia independiente del DataFrame para no modificar el original |

```python
import pandas as pd

df = pd.read_csv('datos.csv')
df_copia = df.copy()   # ¡Siempre guarda una copia de seguridad!
print(df.head())
print(df.shape)
print(df.info())
```

## Paso 2 — Identificar Valores Nulos (NaN)

Los **valores nulos** son datos faltantes que pueden sesgar el análisis y producir resultados incorrectos.

- **`NaN`** *(Not a Number)* → se usa para datos numéricos ausentes
- **`None`** → se usa para datos no numéricos ausentes

### 🔍 Funciones de detección

| Función | Descripción |
|---|---|
| `df.isnull()` | Devuelve un DataFrame booleano con `True` en cada posición donde hay un valor nulo |
| `df.isna()` | Alias exacto de `isnull()`. Ambos son intercambiables |
| `df.notnull()` | Inverso de `isnull()`: devuelve `True` donde el valor **no** es nulo |
| `df.isnull().sum()` | Cuenta el número de nulos por columna. Muy útil para una visión rápida |
| `df.isnull().sum().sum()` | Cuenta el total de nulos en todo el DataFrame |
| `df.isnull().mean()` | Calcula el porcentaje de nulos por columna (en decimal, p. ej. `0.15` = 15%) |

```python
# Ver cuántos nulos hay por columna
print(df.isnull().sum())

# Ver el porcentaje de nulos por columna
print(df.isnull().mean().round(2))
```


## Paso 3 — Manejar Valores Nulos

No existe una estrategia única: la elección depende del contexto, el volumen de nulos y el tipo de análisis.

### 🗑️ Eliminación

| Función | Descripción |
|---|---|
| `df.dropna()` | Elimina **filas** que contengan al menos un valor nulo |
| `df.dropna(axis=1)` | Elimina **columnas** que contengan al menos un valor nulo |
| `df.dropna(how='all')` | Elimina filas solo si **todos** sus valores son nulos |
| `df.dropna(thresh=n)` | Elimina filas que tengan menos de `n` valores no nulos |
| `df.dropna(subset=['col'])` | Elimina filas con nulos únicamente en las columnas indicadas |

### 🩹 Imputación (rellenar)

| Función | Descripción |
|---|---|
| `df.fillna(0)` | Rellena todos los nulos con el valor `0` (o cualquier constante) |
| `df.fillna('Desconocido')` | Rellena nulos de columnas de texto con una cadena específica |
| `df['col'].fillna(df['col'].mean())` | Rellena nulos con la **media** de la columna (conserva distribución) |
| `df['col'].fillna(df['col'].median())` | Rellena nulos con la **mediana** (más robusta ante outliers) |
| `df['col'].fillna(df['col'].mode()[0])` | Rellena nulos con la **moda** (valor más frecuente, útil en categóricas) |
| `df.fillna(method='ffill')` | *Forward fill*: propaga el último valor válido hacia adelante |
| `df.fillna(method='bfill')` | *Backward fill*: propaga el siguiente valor válido hacia atrás |

> ⚠️ **Precaución:** La eliminación puede hacer perder información valiosa. Considera siempre el contexto antes de elegir la estrategia.

```python
# Imputar con la media de cada columna numérica
df_imputado = df.fillna(df.mean(numeric_only=True))

# Imputar columna específica con la mediana
df['edad'] = df['edad'].fillna(df['edad'].median())
```


## Paso 4 — Identificar Filas Duplicadas

Las filas duplicadas distorsionan cualquier análisis: inflan conteos, sesgan promedios y perjudican modelos de machine learning.

### 🔍 Funciones de detección

| Función | Descripción |
|---|---|
| `df.duplicated()` | Devuelve una Serie booleana: `True` en las filas que son duplicadas |
| `df.duplicated().sum()` | Cuenta el número total de filas duplicadas |
| `df[df.duplicated()]` | Muestra todas las filas duplicadas para inspección visual |
| `df.duplicated(subset=['col'])` | Busca duplicados solo considerando las columnas indicadas |
| `df.duplicated(keep='first')` | Marca como duplicadas todas las ocurrencias excepto la primera (por defecto) |
| `df.duplicated(keep='last')` | Marca como duplicadas todas las ocurrencias excepto la última |
| `df.duplicated(keep=False)` | Marca **todas** las ocurrencias de un duplicado, incluida la primera |

```python
# ¿Cuántos duplicados hay?
print(df.duplicated().sum())

# Ver las filas duplicadas
print(df[df.duplicated()])
```

## Paso 5 — Eliminar Filas Duplicadas

### 🧹 Funciones de eliminación

| Función | Descripción |
|---|---|
| `df.drop_duplicates()` | Elimina filas completamente idénticas; conserva la primera ocurrencia |
| `df.drop_duplicates(keep='last')` | Conserva la **última** ocurrencia en lugar de la primera |
| `df.drop_duplicates(keep=False)` | Elimina **todas** las filas duplicadas, sin conservar ninguna |
| `df.drop_duplicates(subset=['col1', 'col2'])` | Elimina duplicados basándose solo en las columnas indicadas |
| `df.drop_duplicates(inplace=True)` | Modifica el DataFrame **en el lugar**, sin necesidad de reasignar |

```python
df_limpio = df.drop_duplicates()

print(f"Filas originales: {len(df)}")
print(f"Filas tras limpiar: {len(df_limpio)}")
print(f"Duplicados eliminados: {len(df) - len(df_limpio)}")
```

> 💡 **Nota:** Por defecto, `drop_duplicates()` retorna un **nuevo** DataFrame. Usa `inplace=True` si quieres modificar el original directamente.


## Flujo Completo de Limpieza

```python
import pandas as pd

# 1. Cargar datos
df = pd.read_csv('datos.csv')
df_original = df.copy()   # Copia de seguridad

# 2. Explorar el dataset
print(df.shape)
print(df.info())

# 3. Detectar nulos
print(df.isnull().sum())

# 4. Imputar valores nulos
df['columna_numerica'] = df['columna_numerica'].fillna(df['columna_numerica'].median())
df['columna_texto'] = df['columna_texto'].fillna('Desconocido')

# 5. Detectar y eliminar duplicados
print(f"Duplicados encontrados: {df.duplicated().sum()}")
df = df.drop_duplicates()

# 6. Verificar resultado
print(f"Nulos restantes: {df.isnull().sum().sum()}")
print(f"Duplicados restantes: {df.duplicated().sum()}")
print(f"Shape final: {df.shape}")
```


## Buenas Prácticas

| Práctica | Descripción |
|---|---|
| 🔒 **Copia de seguridad** | Guarda siempre `df.copy()` antes de aplicar transformaciones irreversibles |
| 🔄 **Automatización** | Encapsula la limpieza en funciones reutilizables para aplicar el mismo proceso a múltiples datasets |
| 🎯 **Contexto** | La mejor estrategia (eliminar vs. imputar) depende del dataset, tipo de datos y objetivo del análisis |
| ✅ **Validación** | Compara dimensiones y estadísticos antes y después de la limpieza |
| 📝 **Documentación** | Comenta el código para justificar cada decisión de limpieza |



## Cheatsheet — Referencia Rápida

┌─────────────────────┬──────────────────────────┬──────────────────────────┬────────────────────────────────┐
│ 📂 CARGA            │ 🔍 EXPLORAR              │ ⚠️ NULOS                 │ 🔁 DUPLICADOS                │
├─────────────────────┼──────────────────────────┼──────────────────────────┼────────────────────────────────┤
│ pd.read_csv()       │ df.shape                 │ df.isnull().sum()        │ df.duplicated().sum()          │
│ pd.read_excel()     │ df.info()                │ df.dropna()              │ df[df.duplicated()]            │
│ pd.read_json()      │ df.describe()            │ df.dropna(how='all')     │ df.drop_duplicates()           │
│ pd.read_sql()       │ df.head() / df.tail()    │ df.fillna(valor)         │ df.drop_duplicates(keep='last')│
│ df.copy()           │ df.dtypes / df.columns   │ df['c'].fillna(media)    │ df.drop_duplicates(keep=False) │
└─────────────────────┴──────────────────────────┴──────────────────────────┴────────────────────────────────┘



## Recursos

### 📖 Documentación oficial
- [Pandas Documentation](https://pandas.pydata.org/docs/) — Referencia completa de la librería
- [Pandas User Guide — Missing Data](https://pandas.pydata.org/docs/user_guide/missing_data.html) — Guía oficial sobre valores nulos
- [Pandas User Guide — Duplicates](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop_duplicates.html) — Referencia de `drop_duplicates`
- [Jupyter Notebooks](https://jupyter.org/) — Sitio oficial y documentación

### 🎓 Cursos y tutoriales
- [Kaggle — Pandas Course (gratis)](https://www.kaggle.com/learn/pandas) — Curso interactivo muy recomendado para iniciarse
- [Real Python — Pandas](https://realpython.com/learning-paths/pandas-data-science/) — Tutoriales en profundidad
- [W3Schools Pandas](https://www.w3schools.com/python/pandas/) — Referencia rápida con ejemplos

### 📊 Datasets de práctica
- [Kaggle Datasets](https://www.kaggle.com/datasets) — Miles de datasets reales para practicar
- [UCI Machine Learning Repository](https://archive.ics.uci.edu/) — Datasets clásicos de ML

### 🛠️ Herramientas complementarias
- [Google Colab](https://colab.research.google.com/) — Jupyter Notebooks en la nube, sin instalación
- [ydata-profiling](https://github.com/ydataai/ydata-profiling) — Genera reportes automáticos de calidad de datos


## 🎮 ¡Pon a prueba tus conocimientos!

¿Crees que has dominado los conceptos? ¡Compruébalo con este Kahoot!

👉 **[Jugar al Kahoot — Jupyter Pandas Inicial](https://create.kahoot.it/preview/3a772d01-29f1-4b7d-a269-d69b7ef2d977)**

Repasa los conceptos clave antes de jugar:
- Funciones de carga de datos (`read_csv`, `read_excel`…)
- Detección y tratamiento de valores nulos (`isnull`, `fillna`, `dropna`)
- Identificación y eliminación de duplicados (`duplicated`, `drop_duplicates`)

> 💬 *"La calidad de tu análisis depende directamente de la calidad de tus datos.
> ¡Invierte tiempo en la limpieza para obtener resultados confiables!"*
