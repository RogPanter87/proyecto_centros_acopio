# Localización óptima de centros de acopio agrícola en Tamaulipas

## Descripción

Este proyecto aborda el problema de localización de centros de acopio agrícola en Tamaulipas mediante técnicas de cómputo evolutivo. Para ello, se utilizan datos de producción agrícola de los 43 municipios del estado y las distancias entre sus cabeceras municipales.

El problema se estudia inicialmente mediante una formulación monoobjetivo orientada a minimizar la distancia promedio ponderada por producción. Posteriormente, se emplea optimización multiobjetivo mediante NSGA-II para considerar simultáneamente la eficiencia logística, la cobertura territorial y el número de centros de acopio.

El desarrollo se realizó en Python utilizando PYMOO como framework principal para la implementación de los algoritmos evolutivos.

## Objetivos de optimización

El proyecto considera tres criterios principales:

- **f1 - Distancia promedio ponderada:** minimizar la distancia entre los municipios productores y su centro de acopio más cercano, considerando la producción agrícola de cada municipio como ponderación.
- **f2 - Distancia máxima de atención:** minimizar la mayor distancia existente entre un municipio y su centro de acopio más cercano.
- **f3 - Número de centros:** minimizar la cantidad de centros de acopio utilizados, considerando configuraciones de entre 3 y 10 instalaciones.

## Metodología

El desarrollo experimental se divide en las siguientes etapas:

1. Carga y análisis exploratorio de los conjuntos de datos.
2. Preparación de la matriz de distancias entre municipios.
3. Formulación del problema de optimización.
4. Implementación de líneas base:
   - Selección aleatoria.
   - Selección basada en producción.
   - Heurística voraz.
5. Implementación de un algoritmo genético monoobjetivo.
6. Comparación del algoritmo genético con las líneas base.
7. Evaluación estadística mediante la prueba de Wilcoxon.
8. Formulación multiobjetivo mediante NSGA-II con dos objetivos.
9. Extensión del problema mediante la incorporación del número de centros como tercer objetivo.
10. Análisis de los frentes de Pareto y del efecto del número de instalaciones.

## Datos

El proyecto utiliza dos conjuntos de datos principales.

### `municipios_productores.csv`

Contiene información correspondiente a los 43 municipios considerados en el problema.

Las variables incluidas son:

- `clave_inegi`: clave INEGI del municipio.
- `municipio`: nombre del municipio.
- `latitud`: latitud de la cabecera municipal.
- `longitud`: longitud de la cabecera municipal.
- `produccion_ton`: producción agrícola en toneladas.

El conjunto contiene **43 registros y 5 variables**.

### `distancias.csv`

Contiene las distancias entre las cabeceras municipales.

Las variables incluidas son:

- `origen`: municipio de origen.
- `destino`: municipio de destino.
- `distancia_km`: distancia entre ambos municipios expresada en kilómetros.

El conjunto contiene **1849 registros**, correspondientes a las 43 × 43 combinaciones entre municipios.

Durante el análisis exploratorio no se identificaron valores nulos en ninguno de los dos conjuntos de datos. Se identificaron 34 municipios con producción positiva y 9 municipios con producción igual a cero. No se encontraron valores negativos de producción.

## Configuración experimental

Los experimentos fueron realizados bajo las siguientes condiciones:

- **Sistema operativo:** Windows 10
- **Arquitectura:** AMD64
- **Procesador:** Intel64 Family 6 Model 140 Stepping 1, GenuineIntel
- **Núcleos lógicos:** 8
- **Memoria RAM:** 15.65 GB
- **Python:** 3.11.9
- **NumPy:** 2.2.6
- **Pandas:** 2.3.3
- **SciPy:** 1.16.3
- **Matplotlib:** 3.10.7
- **PYMOO:** 0.6.2

## Instalación

Se recomienda utilizar **Python 3.11.9** y crear un entorno virtual antes de instalar las dependencias.

Crear el entorno virtual:

```bash
python -m venv .venv
```

Activar el entorno en Windows:

```bash
.venv\Scripts\activate
```

Instalar las dependencias:

```bash
pip install -r requirements.txt
```

## Ejecución

El análisis completo se encuentra implementado en el notebook principal del proyecto. Para reproducir los experimentos, se debe abrir el archivo `.ipynb` mediante Jupyter Notebook, JupyterLab o Visual Studio Code y ejecutar las celdas en el orden establecido.

Para iniciar Jupyter Notebook desde una terminal:

```bash
jupyter notebook
```

Los archivos `municipios_productores.csv` y `distancias.csv` deben encontrarse en las rutas especificadas dentro del notebook.

## Líneas base

Para establecer referencias de comparación se implementaron tres estrategias.

La **selección aleatoria** selecciona aleatoriamente los municipios que funcionan como centros de acopio. Para esta estrategia se realizaron 30 corridas independientes utilizando semillas de 0 a 29.

La **selección basada en producción** selecciona como centros los municipios con mayor producción agrícola.

La **heurística voraz** incorpora progresivamente los centros que producen la mayor reducción de la función objetivo.

Las líneas base se evaluaron para las siguientes configuraciones:

- `p = 3`
- `p = 5`
- `p = 7`
- `p = 10`

## Optimización monoobjetivo

La primera formulación busca minimizar la distancia promedio ponderada por producción.

Se implementó un algoritmo genético para encontrar configuraciones de centros de acopio que minimizaran esta función objetivo. Se evaluaron de manera independiente configuraciones con 3, 5, 7 y 10 centros.

Para cada configuración se realizaron **30 corridas independientes**, utilizando semillas de 0 a 29.

El desempeño del algoritmo genético se comparó con las tres líneas base implementadas.

## Resultados del algoritmo genético

Los mejores valores de distancia promedio ponderada obtenidos mediante el algoritmo genético fueron:

| Número de centros | Distancia promedio ponderada |
|---:|---:|
| 3 | 32.5614 km |
| 5 | 16.6217 km |
| 7 | 9.6398 km |
| 10 | 3.7218 km |

Las 30 corridas independientes convergieron al mismo valor de la función objetivo y a la misma configuración de centros para cada valor de `p`.

Respecto a la selección aleatoria, el algoritmo genético redujo la distancia promedio ponderada en:

| Número de centros | Mejora respecto a selección aleatoria |
|---:|---:|
| 3 | 72.91 % |
| 5 | 78.81 % |
| 7 | 86.97 % |
| 10 | 92.36 % |

La prueba de Wilcoxon obtuvo un valor `p = 1.862645 × 10⁻⁹` para las cuatro configuraciones, indicando diferencias estadísticamente significativas respecto a la selección aleatoria con un nivel de significancia de 0.05.

El algoritmo genético superó a la heurística voraz para tres centros e igualó su desempeño para 5, 7 y 10 centros.

## Optimización multiobjetivo con NSGA-II

Posteriormente, el problema se extendió mediante NSGA-II para optimizar simultáneamente dos criterios:

- **f1:** distancia promedio ponderada por producción.
- **f2:** distancia máxima de atención.

Esta formulación permite obtener un conjunto de soluciones no dominadas que representan diferentes compromisos entre eficiencia logística y cobertura territorial.

La configuración experimental utilizada para NSGA-II fue:

- **Tamaño de población:** 100 individuos.
- **Número de generaciones:** 200.
- **Probabilidad de cruce:** 0.90.
- **Probabilidad de mutación:** 1/43 ≈ 0.023256.
- **Número de corridas:** 30.
- **Semillas:** 0 a 29.

Se evaluaron inicialmente configuraciones independientes para `p = 3`, `p = 5`, `p = 7` y `p = 10`.

## Optimización con tres objetivos

Finalmente, el número de centros se incorporó como un tercer objetivo:

- **f1:** minimizar la distancia promedio ponderada.
- **f2:** minimizar la distancia máxima de atención.
- **f3:** minimizar el número de centros.

En esta formulación, el número de instalaciones dejó de establecerse previamente y pasó a formar parte del proceso de optimización. Se permitieron configuraciones de entre **3 y 10 centros de acopio**.

Las soluciones no dominadas obtenidas incluyeron configuraciones para todos los números de centros dentro del intervalo permitido.

## Resultados de la optimización con tres objetivos

El incremento en el número de centros produjo una reducción general de las distancias de atención, aunque las mejoras no fueron uniformes.

Los mejores valores encontrados para cada cantidad de centros fueron:

| Centros | Menor f1 (km) | Menor f2 (km) |
|---:|---:|---:|
| 3 | 32.5614 | 138.0459 |
| 4 | 20.4873 | 125.2327 |
| 5 | 16.6217 | 106.0083 |
| 6 | 12.7804 | 95.2291 |
| 7 | 9.6398 | 81.4828 |
| 8 | 6.5049 | 77.8516 |
| 9 | 4.5419 | 64.3055 |
| 10 | 3.7921 | 64.3055 |

La distancia promedio ponderada disminuyó progresivamente conforme aumentó el número de instalaciones.

La menor distancia máxima de atención disminuyó hasta alcanzar **64.3055 km con nueve centros**. Al incorporar un décimo centro, este valor permaneció sin cambios, aunque la distancia promedio ponderada continuó disminuyendo de 4.5419 km a 3.7921 km.

Estos resultados muestran el compromiso existente entre eficiencia logística, cobertura territorial y cantidad de infraestructura.

## Reproducibilidad

Los experimentos estocásticos utilizan semillas explícitas para facilitar la reproducción de los resultados.

En los experimentos principales se utilizaron **30 semillas**, correspondientes a los valores comprendidos entre 0 y 29.

Los resultados obtenidos durante los experimentos se almacenan en archivos CSV para facilitar su análisis posterior y la generación de tablas y figuras.

Para reproducir el proyecto se recomienda:

1. Utilizar Python 3.11.9.
2. Instalar las dependencias especificadas en `requirements.txt`.
3. Mantener los conjuntos de datos en las rutas utilizadas por el notebook.
4. Ejecutar las celdas del notebook en orden.
5. Mantener las semillas especificadas en cada experimento.

## Estructura del proyecto

La estructura general del proyecto es la siguiente:

```text
Proyecto_Final/
│
├── README.md
├── requirements.txt
├── dataset/
│   └── municipios_productores.csv
│   └── distancias.csv
├── Proyecto_Final.ipynb
│
└── paper/
│   ├── articulo.tex
│   ├── articulo.pdf
│   ├── articulo.aux
│   ├── articulo.log
│   ├── articulo.synctex.gz
│   ├── evolucion_f1_f2_numero_centros.png
│   ├── frente_pareto_p3.png
│   ├── frente_pareto_p5.png
│   ├── frente_pareto_p7.png
│   └── frente_pareto_p10.png
│
├── results/
│   ├── comparacion_lineas_base.csv
│   ├── frente_pareto_3_objetivos.csv
│   ├── frente_pareto_p10.csv
│   ├── frente_pareto_p3.csv
│   ├── frente_pareto_p5.csv
│   ├── frente_pareto_p7.csv
│   ├── mejores_soluciones_ga.csv
│   ├── numero_soluciones_nsga2.csv
│   ├── resultados_ga_monoobjetivo.csv
│   ├── resultados_heuristica_produccion.csv
│   ├── resultados_heuristica_voraz.csv
│   ├── resultados_linea_base_aleatoria.csv
│   ├── resultados_nsga2_2_objetivos.csv
│   ├── resultados_nsga2_3_objetivos.csv
│   ├── resumen_ga_monoobjetivo.csv
│   ├── resumen_linea_base_aleatoria.csv
│   ├── resumen_nsga2_3_objetivos.csv
│   ├── resumen_soluciones_nsga2.csv
│   ├── soluciones_representativas_nsga2.csv
│   └── wilcoxon_ga_vs_aleatorio.csv
│
├── figures/
│   ├── evolucion_f1_f2_numero_centros.png
│   ├── frente_pareto_p3.png
│   ├── frente_pareto_p5.png
│   ├── frente_pareto_p7.png
│   └── frente_pareto_p10.png
```


## Requisitos

Las principales dependencias utilizadas se encuentran especificadas en el archivo `requirements.txt`:

```text
numpy==2.2.6
pandas==2.3.3
scipy==1.16.3
matplotlib==3.10.7
pymoo==0.6.2
psutil
jupyter
ipykernel
```

## Autor

**Carlos Emanuel Ruiz Colunga**

Maestría en Ciencias e Ingeniería de Datos  
Facultad de Ingeniería y Ciencias  
Universidad Autónoma de Tamaulipas

## Asignatura

**Cómputo Evolutivo**

## Docente

**Dr. Alan Díaz Manríquez**
