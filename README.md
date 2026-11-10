# Predicción del interés en Informática, Tecnología, Programación o Ciencia de Datos (IDES-KNAC 2023)

Modelo de regresión logística para predecir si un joven argentino de 16 a 18 años declara que piensa estudiar Informática, Tecnología, Programación o Ciencia de Datos, e identificar qué factores socioeducativos y actitudinales se asocian con ese interés.

---

## 1. Objetivo del Proyecto

El propósito de este trabajo es explorar, preprocesar y construir un modelo de clasificación que permita anticipar si un joven de 16 a 18 años declara que piensa estudiar Informática, Tecnología, Programación o Ciencia de Datos, a partir de los microdatos del relevamiento IDES-KNAC 2023, e identificar qué respuestas se asocian con ese interés.

**Objetivos específicos**

1. Preparar los datos de la encuesta y definir la variable objetivo.
2. Explorar los datos y ver cómo se reparte el interés.
3. Entrenar una regresión logística y elegir sus hiperparámetros y el umbral con validación cruzada.
4. Evaluarla en jóvenes que no vio, comparándola con un modelo de referencia y con el modelo por defecto.
5. Identificar las variables que más se asocian con el interés.

---

## 2. Estructura del Repositorio

El proyecto se organiza bajo la arquitectura estándar de Cookiecutter Data Science para garantizar la reproducibilidad y el orden analítico:

```text
├── data/
│   ├── raw/                                   <- Datos crudos originales y diccionario de variables
│   │   ├── estudio_stem_usuaria.csv           <- Dataset principal crudo
│   │   └── libro_de_variables_usuaria-_stem.xlsx <- Diccionario oficial de variables
│   ├── interim/                               <- Partición entrenamiento / prueba
│   │   ├── X_train.csv
│   │   └── X_test.csv
│   └── processed/                             <- Datos preparados para el modelado
│       ├── X_train_prep.csv, X_test_prep.csv
│       └── y_train.csv, y_test.csv
├── notebooks/                                 <- Libretas de Jupyter
│   ├── 01_EDA.ipynb                           <- Preparación y exploración de los datos
│   └── 02_Modelado_regresion_logistica.ipynb  <- Modelo, hiperparámetros, umbral y resultados
├── models/                                    <- Modelo entrenado
│   └── regresion_logistica.joblib
├── docs/                                      <- Informe del proyecto
│   └── informe_final.md
├── reports/
│   └── figures/                               <- Gráficos del informe
├── .gitignore                                 <- Filtros para omitir archivos temporales
├── LICENSE                                    <- Términos de uso del código (Licencia MIT)
├── README.md                                  <- Descripción general del proyecto
└── requirements.txt                           <- Dependencias para replicar el entorno de ejecución
```

**Orden de lectura:** `docs/informe_final.md`, luego `notebooks/01_EDA.ipynb` y `notebooks/02_Modelado_regresion_logistica.ipynb`.

---

## 3. Población y Estructura del Dataset

Para el modelado predictivo se aplicó un protocolo de delimitación poblacional, depuración de datos y prevención de fuga de información (*data leakage*).

### 3.1 Delimitación Poblacional y Variable Objetivo ("y")

* **Población Analítica Principal:** Partiendo de una base bruta de $2.891$ registros, se aplicó el filtro de intención educativa (`P12`, reteniendo $2.386$ casos) y se excluyeron expresamente $267$ registros con indecisión vocacional declarada (`P13_12 = "No sé aún"`), quedando $2.119$ jóvenes. Los resultados no se pueden generalizar a los indecisos.
* **Variable Objetivo ($y$):** Intención declarada de estudiar Informática, Tecnología, Programación o Ciencia de Datos (`P13_9`, opción "Pensás seguir estudiando Informática / Tecnología / Programación / Ciencia de Datos"):
  * **$y = 1$:** El encuestado seleccionó expresamente dicha área tecnológica ($359$ casos, $16,9\%$).
  * **$y = 0$:** Optó por otras alternativas vocacionales ($1.760$ casos).
* **Partición:** $80/20$ estratificada (`random_state=42`): $1.695$ jóvenes para entrenamiento y $424$ para prueba.

---

### 3.2 Dimensiones de la Matriz Predictora ($X$)

La matriz de características procesa un total de **$125$ variables predictoras de entrada** ($114$ respuestas de la encuesta y $11$ indicadores creados) que, tras la aplicación del *Pipeline* de transformación e imputación (incluyendo codificación *One-Hot*), expanden la matriz final a **$208$ columnas predictoras**:

| Dimensión | Variables Clave | Descripción Metodológica |
| :--- | :--- | :--- |
| **Demografía y Contexto** | `REGION`, `F4.SEXO`, `F1.Edad`, `P28`, `P31`, `P32`, `P33a`, `P38` | Ubicación geográfica, edad, sexo registrado, país de nacimiento, internet y computadora en casa, integrantes del hogar y habitaciones para dormir. |
| **Escuela** | `P4`, `P23`, `P24`, `P26`, `P27` | Situación escolar, tipo de escuela, año, si es técnica y orientación que cursa. |
| **Actitudes, Referentes e Intereses** | Bloques `P2_*`, `P3_*`, `P19`, `P20`, `P22_*`, `P35`, `P36`, `P37` | Tiempo dedicado a actividades, interés en ciencia y tecnología, nivel educativo del sostén del hogar y de la madre, y características de la persona referente en ciencia y tecnología. |
| **Desempeño y Experiencia Escolar** | Bloques `P5_*`, `P6_*`, `P7_*`, `P8_*`, `P9_*`, `P10_*`, `P11_*` | Gusto, valoración y notas en las materias; cómo describe las materias y a sus docentes; recursos usados en clase y participación en talleres, tutorías y concursos. |
| **Indicadores Especiales Creados** | `no_tiene_*`, `no_sabe_*` | Variables binarias (0/1) que capturan la ausencia de materias en el colegio o respuestas de incertidumbre informativa. |

---

### 3.3 Criterios de Exclusión y Control de Fuga de Información (*Data Leakage*)

Para garantizar la capacidad de generalización del modelo ante datos no observados, se removieron explícitamente ($101$ columnas en total):

1. **Identificadores y Ponderadores:** `ID`, `POND_SIN_ELEVAR` (reservados exclusivamente para análisis descriptivos ponderados).
2. **Variables con exceso de nulos:** `P25` (excluida por presentar más del $81\%$ de valores faltantes) y `P21` (más del $65\%$).
3. **Variables de Intención Simultánea:** Opciones excluyentes de `P13` (distintas de `P13_9`) y variables de postulación o control posterior (`P15a`, `P16`, `P18`), que dependen de la respuesta, evitando alterar la temporalidad predictiva.

> *Nota: La documentación detallada de cada código, escala Likert y valor codificado se encuentra disponible en el archivo complementario [`libro_de_variables_usuaria-_stem.xlsx`](./data/raw/libro_de_variables_usuaria-_stem.xlsx).*

---

## 4. Modelo

* **Algoritmo:** Regresión logística (scikit-learn) dentro de un `Pipeline`: imputación de faltantes, estandarización, One-Hot y regresión logística.
* **Hiperparámetros:** `GridSearchCV` (validación cruzada de $5$ particiones, métrica F1) sobre $10$ combinaciones de `C` ($0,001$ / $0,01$ / $0,1$ / $1$ / $10$) y `class_weight` (`None` o `'balanced'`). Elegido: `C = 0,01` con `class_weight='balanced'` (F1 de validación $0,561$).
* **Umbral de decisión:** $0,50$, elegido con validación cruzada sobre el entrenamiento como el de mayor precisión entre los que tienen recall de al menos $0,70$.
* **Métricas:** exactitud, precisión, recall, F1, ROC-AUC y matriz de confusión.

---

## 5. Resultados

Evaluación en la prueba ($424$ jóvenes: $72$ "Sí" y $352$ "No"):

| Modelo | Exactitud | Precisión | Recall | F1 |
| :--- | :---: | :---: | :---: | :---: |
| Referencia (siempre "No") | 0,830 | — | 0,000 | — |
| Por defecto (`C = 1`, sin ponderar) | 0,858 | 0,630 | 0,403 | 0,492 |
| **Elegido, umbral 0,50** | 0,823 | 0,485 | 0,681 | 0,566 |
| Elegido, umbral 0,65 | 0,866 | 0,623 | 0,528 | 0,571 |

* El modelo elegido detecta $49$ de los $72$ jóvenes interesados, con $52$ falsas alarmas. ROC-AUC: $0,85$.
* Frente al modelo por defecto detecta casi $7$ de cada $10$ interesados en lugar de $4$ de cada $10$.
* En la prueba el recall ($0,68$) quedó algo por debajo del $0,70$ buscado en validación; son jóvenes distintos y la prueba tiene solo $72$ interesados. El umbral no se reajustó con la prueba.

### Variables que más pesan

| Aumentan la probabilidad de interés | Reducen la probabilidad de interés |
| :--- | :--- |
| Interés en hacer cursos de programación o robótica | No haber participado ni querer hacerlo en tutorías para olimpíadas de informática |
| Que guste la materia Informática / computación | Ser mujer |
| Considerar que Computación / Informática / Tecnología sirve para el futuro laboral | No haber participado ni querer hacerlo en talleres de robótica / programación |

Son asociaciones, no causas. Varias de estas variables miden interés o experiencia en informática, muy cerca de lo que se predice.

---

## 6. Conclusiones y Limitaciones

La regresión logística detecta a casi $7$ de cada $10$ jóvenes interesados, pero con muchas falsas alarmas. Sirve como un primer filtro para ordenar a los jóvenes por probabilidad de interés, no para decidir sobre una persona, y no se aplica a los indecisos.

* Muestra online y no probabilística; quedan afuera los indecisos y quienes no piensan seguir estudiando.
* Predictores y objetivo se respondieron al mismo tiempo: no se sabe si lo declarado coincide con lo que después estudian.
* Solo $72$ "Sí" en la prueba y una única partición $80/20$: las métricas pueden variar con otra división.

---

## 7. Cómo Reproducir

```bash
pip install -r requirements.txt
```

Ejecutar en orden `notebooks/01_EDA.ipynb` y `notebooks/02_Modelado_regresion_logistica.ipynb`, con rutas relativas desde `notebooks/`.

---

## 8. Referencias

* IDES-KNAC / MINCyT (2023). Encuesta "Actitud de los jóvenes hacia las profesiones STEM" (base de microdatos y libro de variables). Portal argentina.gob.ar. Descargada el 3 de octubre de 2026.
* Pedregosa et al. (2011). Scikit-learn: Machine Learning in Python. *Journal of Machine Learning Research*, 12, 2825–2830.

---

*Materia: Aprendizaje Automático | Alumno: Mauricio Guillermo Turdo | Profesor: Nicolás Caballero | 2026*
