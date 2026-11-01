# 📊 Análisis Predictivo de Intención Vocacional en Tecnología (IDES-KNAC 2023)

Sistema de análisis y modelado predictivo para identificar factores socioeducativos y actitudinales asociados a la intención de estudio en áreas de Tecnología, Programación e Informática en jóvenes argentinos.

---

## 🎯 Objetivo del Proyecto

El propósito de este trabajo es explorar, preprocesar y construir modelos de clasificación que permitan anticipar la preferencia vocacional por disciplinas tecnológicas a partir de microdatos del relevamiento 2023.

---

## 📁 Estructura del Repositorio

El proyecto se organiza de forma modular para garantizar la reproducibilidad y el orden analítico:


├── data/
│   ├── raw/                              <- Datos crudos originales del relevamiento
│   │   ├── estudio_stem_usuaria.csv      <- Dataset principal con microdatos de encuestas
│   │   └── libro_de_variables_usuaria-_stem.xlsx <- Diccionario de variables del estudio
│   └── processed/                        <- Datos limpios y preprocesados listos para el modelado
├── cuadernos/                            <- Libretas de Jupyter / RStudio con el análisis exploratorio y preprocesamiento
├── documentos/                           <- Informes detallados del proyecto (informe_2.md e informe preliminar)
├── modelos/                              <- Archivos binarios de modelos entrenados (futuros .pkl / .joblib)
├── src/                                  <- Scripts auxiliares en Python y funciones reutilizables
├── .gitignore                            <- Filtros para omitir archivos temporales y datos pesados
├── LICENCIA                              <- Términos de uso del código (Licencia MIT)
├── LÉAME.md                              <- Portada del proyecto y guía de navegación
└── requisitos.txt                        <- Dependencias y librerías necesarias para replicar el entorno


## 📊 Población y Estructura del Dataset (`dt_filtrado`)

Para el entrenamiento del modelo de Regresión Logística, se aplicó un protocolo riguroso de delimitación poblacional, depuración de datos y prevención de fuga de información (*data leakage*).

### 🎯 Delimitación Poblacional y Variable Objetivo ($y$)
* **Población Válida ($N = 2.386$):** Se filtró la base original seleccionando exclusivamente a estudiantes del nivel secundario en curso (`P12`), excluyendo aquellos registros con incertidumbre vocacional declarada (`P13_12 = "No sé aún"`).
* **Variable Objetivo ($y$):** Intención declarada de estudiar o trabajar en áreas de **Informática / Programación / Sistemas** (`P13_9`):
  * **$y = 1$:** Intención positiva de optar por la carrera/área.
  * **$y = 0$:** Sin intención declarada.

---

### 🧩 Dimensiones del Matriz Predictora ($X$)
La matriz de características $X$ agrupa más de 180 columnas originales (posteriormente codificadas e imputadas dentro del *Pipeline* de Scikit-Learn) categorizadas en 4 bloques principales:

| Dimensión | Variables Clave | Descripción Metodológica |
| :--- | :--- | :--- |
| **Demografía y Contexto** | `REGION`, `F4.SEXO`, `F1.Edad`, `P20`, `P21`, `P27`, `P38` | Ubicación geográfica, edad, sexo registrado, nivel educativo de los padres, equipamiento del hogar y hacinamiento residencial. |
| **Actitudes e Intereses STEM** | Bloques `P2_*`, `P3_*`, `P36`, `P37` | Tiempo dedicado a actividades tecnológicas/videojuegos, hábito de lectura, consumo de contenidos científicos y percepción de autoeficacia lógica. |
| **Desempeño y Experiencia Escolar** | Bloques `P5_*`, `P6_*`, `P7_*` | Desempeño, gusto y experiencia previa en materias clave (Matemática, Computación, Física, Química). |
| **Indicadores Especiales Creados** | `no_tiene_*`, `no_sabe_*` | Variables binarias (0/1) que capturan explícitamente la ausencia de materias en el colegio o respuestas ambiguas, evitando la pérdida masiva de observaciones. |

---

### 🛡️ Criterios de Exclusión y Control de Fuga de Información (*Data Leakage*)
Para garantizar un modelo con capacidad de generalización real ante datos no observados, se removieron explícitamente:
1. **Identificadores y Ponderadores:** `ID`, `POND_SIN_ELEVAR` (reservados exclusivamente para análisis descriptivos ponderados).
2. **Variables de Intención Posterior:** Bloques `P13_1` a `P13_11` (salvo la target `P13_9`), `P15`, `P16`, `P18`, `P39`, `P33b` y `P33c`, ya que corresponden a decisiones o postulaciones simultáneas/posteriores que invalidarían la temporalidad predictiva.

> 📄 *Nota: La documentación detallada de cada código, escala Likert y valor codificado se encuentra disponible en el archivo complementario [`libro_de_variables_usuaria-_stem.xlsx`](./libro_de_variables_usuaria-_stem.xlsx).*