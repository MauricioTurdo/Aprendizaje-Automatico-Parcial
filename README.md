# Análisis Predictivo de Intención Vocacional en Tecnología (IDES-KNAC 2023)

Sistema de análisis y modelado predictivo para identificar factores socioeducativos y actitudinales asociados a la elección de carreras en áreas de Tecnología, Programación e Informática en jóvenes argentinos.

---

## 1. Objetivo del Proyecto

El propósito de este trabajo es explorar, preprocesar y construir modelos de clasificación que permitan anticipar la preferencia vocacional por disciplinas tecnológicas a partir de los microdatos del relevamiento IDES-KNAC 2023.

---

## 2. Estructura del Repositorio

El proyecto se organiza bajo la arquitectura estándar modular para garantizar la reproducibilidad y el orden analítico:

```text
├── data/
│   ├── raw/                              <- Datos crudos originales y diccionario de variables
│   │   ├── estudio_stem_usuaria.csv      <- Dataset principal Crudo
│   │   └── libro_de_variables_usuaria-_stem.xlsx <- Diccionario oficial de variables
│   └── processed/                        <- Datos limpios y preprocesados listos para el modelado
│       └── dataset_filtrado.csv          <- Dataset Limpio 
├── cuadernos/                            <- Libretas de Jupyter 
│       └── Notebooks_EDA.ipynb           <-  Jupyter de la EDA 
├── documentos/                           <- Informes metodológicos del proyecto (informe_2.md)
│       └── Entrega 1 Corregida - Aprendizaje Automático - Mauricio Turdo.pdf 
│       └── Informe Descripción del Dataset y Origen.md
│       └── informe_preliminar_jovenes_stem.md 
├── modelos/                              <- Archivos binarios de modelos entrenados (.pkl / .joblib)
├── src/                                  <- Scripts auxiliares y funciones reutilizables
├── .gitignore                            <- Filtros para omitir archivos temporales
├── LICENSE                               <- Términos de uso del código (Licencia MIT)
├── README.md                             <- Descripción general del proyecto
└── requirements.txt                      <- Dependencias para replicar el entorno de ejecución
```

---

## 3. Población y Estructura del Dataset

Para el modelado predictivo se aplicó un protocolo de delimitación poblacional, depuración de datos y prevención de fuga de información (*data leakage*).

### 3.1 Delimitación Poblacional y Variable Objetivo ($y$)

* **Población Analítica Principal ($N = 2.119$):** Partiendo de una base bruta de $2.891$ registros, se aplicó el filtro de intención educativa (`P12`, reteniendo $2.386$ casos) y se excluyeron expresamente $267$ registros con indecisión vocacional declarada (`P13_12 = "No sé aún"`).
* **Variable Objetivo ($y$):** Intención declarada de elegir la opción de “Informática / Programación / Sistemas”; la etiqueta real es “Informática / Tecnología / Programación / Ciencia de Datos” (`P13_9`)- "Pensás seguir estudiando Informática / Tecnología / Programación / Ciencia de Datos":
  * **$y = 1$:** El encuestado seleccionó expresamente dicha área tecnológica.
  * **$y = 0$:** Optó por otras alternativas vocacionales.

---

### 3.2 Dimensiones de la Matriz Predictora ($X$)

La matriz de características procesa un total de **$125$ variables predictoras de entrada** que, tras la aplicación del *Pipeline* de transformación e imputación (incluyendo codificación *One-Hot*), expanden la matriz final a **$208$ columnas predictoras**:

| Dimensión | Variables Clave | Descripción Metodológica |
| :--- | :--- | :--- |
| **Demografía y Contexto** | `REGION`, `F4.SEXO`, `F1.Edad`, `P27`, `P38` | Ubicación geográfica, edad, sexo registrado, Habitaciones y condiciones generales del hogar. |
| **Actitudes, Referentes e Intereses** | Bloques `P2_*`, `P3_*`, `P36`, `P37`, `P20` | hábitos de lectura, consumo digital, tiempo dedicado a actividades, nivel educativo del sostén del hogar/madre y características de la persona referente en ciencia y tecnología (`P20`/`P21`). |
| **Desempeño y Experiencia Escolar** | Bloques `P5_*`, `P6_*`, `P7_*` | Desempeño, gusto y experiencia previa en materias clave (Matemática, Computación, Física, Química). |
| **Indicadores Especiales Creados** | `no_tiene_*`, `no_sabe_*` | Variables binarias (0/1) que capturan la ausencia de materias en el colegio o respuestas de incertidumbre informativa. |

---

### 3.3 Criterios de Exclusión y Control de Fuga de Información (*Data Leakage*)

Para garantizar la capacidad de generalización del modelo ante datos no observados, se removieron explícitamente:

1. **Identificadores y Ponderadores:** `ID`, `POND_SIN_ELEVAR` (reservados exclusivamente para análisis descriptivos ponderados).
2. **Variable con exceso de nulos:** `P25` (excluida por presentar más del $82\%$ de valores faltantes).
3. **Variables de Intención Simultánea:** Opciones excluyentes de `P13` (distintas de `P13_9`) y variables de postulación o control posterior (`P15`, `P16`, `P18`), evitando alterar la temporalidad predictiva.

> *Nota: La documentación detallada de cada código, escala Likert y valor codificado se encuentra disponible en el archivo complementario [`libro_de_variables_usuaria-_stem.xlsx`](./data/raw/libro_de_variables_usuaria-_stem.xlsx).*