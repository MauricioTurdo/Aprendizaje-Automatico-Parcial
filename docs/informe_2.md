![Portada del proyecto](../images/portada_proyecto.svg)

# Informe de Proyecto: Aprendizaje Automático Supervisado

**Título:** Sistema predictivo de la intención declarada de elección vocacional en áreas de Informática, Tecnología, Programación y Ciencia de Datos en jóvenes argentinos  
**Alumno:** Mauricio Guillermo Turdo  
**Institución:** Centro Politécnico Superior Malvinas Argentinas  
**Carrera:** Tecnicatura en Ciencia de Datos e Inteligencia Artificial (2° Año)  
**Año:** 2026  

---
  
## Índice
1. [Contexto y Relevancia del Problema](#1-contexto-y-relevancia-del-problema)
2. [Objetivo General y Específicos](#2-objetivo-general-y-específicos)
3. [Tipo de Problema y Definición de la Variable Objetivo](#3-tipo-de-problema-y-definición-de-la-variable-objetivo)
4. [Unidad de Análisis (¿Qué representa cada fila?)](#4-unidad-de-análisis-qué-representa-cada-fila)
5. [Fuentes de Datos y Control de Fuga de Información](#5-fuentes-de-datos-y-control-de-fuga-de-información)
6. [Preprocesamiento y Transformación de Datos](#6-preprocesamiento-y-transformación-de-datos)
7. [Modelado Predictivo (Algoritmos Seleccionados)](#7-modelado-predictivo-algoritmos-seleccionados)
8. [Estrategia de Evaluación y Línea Base (Baseline)](#8-estrategia-de-evaluación-y-línea-base-baseline)
9. [Resultados e Interpretación](#9-resultados-e-interpretación)
10. [Conclusiones, Limitaciones y Alcance Válido](#10-conclusiones-limitaciones-y-alcance-válido)
                      
---
                        
## 1. Contexto y Relevancia del Problema
                        
Con el desarrollo acelerado de las nuevas tecnologías y la irrupción de la inteligencia artificial en el mercado laboral, la promoción de vocaciones en **Informática, Tecnología, Programación y Ciencia de Datos** constituye un eje estratégico para el desarrollo socioeconómico e innovador del país. No obstante, persisten brechas significativas de género, disparidades entre modalidades de educación secundaria y barreras de percepción sobre la complejidad de estas disciplinas. 
                      
Poder anticipar los intereses y preferencias vocacionales de los jóvenes permite a las instituciones educativas y planificadores estatales orientar los recursos de manera eficiente. Este proyecto aborda esta problemática mediante la aplicación de técnicas de Aprendizaje Automático Supervisado sobre datos de encuesta de alcance nacional.
                      
---
                        
## 2. Objetivo General y Específicos
                        
### Objetivo General
Desarrollar y evaluar un modelo de Aprendizaje Automático supervisado para predecir la **intención declarada** de jóvenes de 16 a 18 años de escuelas secundarias de estudiar carreras superiores en el área de la **Informática, Tecnología, Programación y Ciencia de Datos**, a partir de sus atributos socioeducativos, rendimiento académico percibido y actitudes hacia la ciencia y la tecnología.
                      
### Objetivos Específicos
Objetivos Específicos:

1. Analizar, depurar y preprocesar el conjunto de microdatos de la encuesta nacional (IDES-KNAC, 2023) mediante la imputación de valores faltantes, codificación de variables categóricas (región, género, modalidad escolar, percepciones) y exclusión de registros con ambigüedad vocacional.
2. Identificar y seleccionar las variables predictoras clave asociadas con la intención de estudiar disciplinas informáticas, garantizando la exclusión rigurosa de variables que generen fuga de información (data leakage).
3. Implementar y entrenar el algoritmo de clasificación supervisada de Regresión Logística como modelo central del sistema predictivo, aprovechando su alta interpretabilidad directa mediante coeficientes para evaluar el impacto de las características socioeducativas.
4. Evaluar el rendimiento del modelo utilizando métricas de validación como Accuracy, Precision, Recall y F1-Score, contrastándolo frente a una línea base sencilla (baseline de clase mayoritaria).
                      
---
                        
## 3. Tipo de Problema y Definición de la Variable Objetivo
                        
El problema se define como un problema de **Aprendizaje Supervisado de Clasificación Binaria**.
                      
* **¿Qué predice exactamente el modelo?**
  Predice la probabilidad de que un estudiante de escuela secundaria (16 a 18 años) manifieste una **intención declarada** de elegir una carrera superior vinculada al sector de la Informática, Tecnología, Programación o Ciencia de Datos al finalizar sus estudios.
* **Variable Objetivo ($y$):**
  Construida a partir de la opción específica **P13_9** del estudio nacional IDES-KNAC (2023):
  * **$y = 1$**: El estudiante seleccionó expresamente opciones vinculadas a Informática, Tecnología, Programación o Ciencia de Datos.
  * **$y = 0$**: El estudiante no seleccionó dicha opción.
                      
---
                        
## 4. Unidad de Análisis (¿Qué representa cada fila?)
                        
Cada fila o registro dentro del conjunto de datos procesado representa a **un estudiante individual de educación secundaria (de entre 16 y 18 años)** relevado por el estudio *"Actitud de la juventud hacia las profesiones STEM"* (consorcio IDES-KNAC, 2023), el cual abarca un total de 2.880 observaciones distribuidas en las 8 regiones COFECYT.
                      
---
                        
## 5. Fuentes de Datos y Control de Fuga de Información
                        
### Fuente Principal
* **Estudio:** IDES-KNAC (2023). *Actitud de la juventud hacia las profesiones STEM*. Informe preliminar, base de microdatos y diccionario de variables.
                      
### Control estricto de Fuga de Información (*Data Leakage*)
Para garantizar que el modelo aprenda patrones genuinos y no haga predicciones basadas en información trampa, se excluyeron rigurosamente:
* Identificadores y factores de expansión (`ID`, `POND_SIN_ELEVAR`).
* Preguntas que replican de forma directa o indirecta la variable objetivo (`P12`, el subconjunto de respuestas `P13_1` a `P13_12` ajenas al área informática).
* Variables de control futuro o metadatos restringidos (`P15`, `P16`, `P18`, `P39`, `P33b`, `P33c`).
                      
Al momento de realizar la predicción, el modelo cuenta **exclusivamente** con variables de contexto relevadas en el entorno escolar estándar (atributos sociodemográficos, región, género y percepciones actitudinales).
                      
---
                        
## 6. Preprocesamiento y Transformación de Datos
                        
Antes de alimentar los algoritmos, se aplicó un pipeline de ingeniería de datos robusto e idempotente:
1. **Tratamiento de códigos especiales:** Conversión de códigos de omisión o NS/NC (como 5, 6, 99) a valores faltantes (`NaN`) y creación de indicadores de ausencia explícitos (`no_tiene_*`, `no_sabe_*`).
2. **Imputación y Escalamiento:** Uso de `SimpleImputer` (por mediana/moda) y `StandardScaler` para variables numéricas y ordinales.
3. **Codificación Categórica:** Aplicación de `OneHotEncoder` (con `handle_unknown='ignore'`) sobre variables nominales, seguido de un control automatizado de eliminación de columnas duplicadas idénticas para garantizar matrices limpias y perfectamente sincronizadas de 210 características entre `X_train` y `X_test`.
                      
---

## 7. Modelado Predictivo (En desarrollo)

Actualmente el proyecto se encuentra en la etapa de preprocesamiento, análisis exploratorio y preparación de los conjuntos de datos. Próximamente se implementará y evaluará el modelo de Regresión Logística como base predictiva inicial.

---

## 8. Estrategia de Evaluación y Línea Base

- **Preparación de Datos:** Se estructuraron microdatos, transformando las variables predictoras necesarias para alimentar los futuros modelos de clasificación.
- **Criterio de Evaluación:** Se prevé utilizar métricas estándar de clasificación para medir el rendimiento en la predicción de la elección vocacional en tecnología una vez entrenado el modelo.

---
                        
## 9. Resultados e Interpretación
                        
- **Expectativa de Rendimiento:** Se evaluará si el modelo predictivo logra superar consistentemente la línea base de referencia establecida.
- **Análisis Previsto:** Se proyecta analizar los coeficientes de la Regresión Logística para identificar qué variables (como el entorno social, la autopercepción o la curiosidad hacia la tecnología) presentan mayor poder discriminante en la elección vocacional de los jóvenes.                

---

## 10. Conclusiones, Limitaciones y Alcance Válido

### Conclusiones Alcanzables
- **Valor exploratorio:** Es posible identificar y cuantificar qué factores socioeducativos y actitudinales se asocian a la intención declarada de elegir carreras o áreas tecnológicas.
- **Base analítica:** Permite fundamentar decisiones y políticas educativas basadas en datos reales del relevamiento 2023.

### Limitaciones del Estudio
- **Sesgo de no respuesta en la variable objetivo:** La exclusión o presencia de 505 casos sin respuesta en la variable dependiente reduce la muestra efectiva y exige cautela al generalizar los hallazgos a toda la población encuestada.
- **Asociación no es causalidad:** Los hallazgos reflejan relaciones estadísticamente significativas, pero no implican un vínculo de causa y efecto directo.
- **Intención declarada vs. conducta real:** Se evalúa la preferencia manifestada en la encuesta, no la matriculación efectiva ni el desempeño laboral futuro (requeriría un enfoque longitudinal).
- **Alcance temporal y territorial:** Los patrones corresponden exclusivamente al contexto del relevamiento 2023, por lo que no deben extrapolarse linealmente a otros períodos ni regiones sin revalidación.
- **Desbalance de clases en la variable objetivo:** La distribución desigual entre las categorías de la variable dependiente puede generar un sesgo en el aprendizaje del modelo, priorizando la clase mayoritaria y requiriendo técnicas de ajuste (como ponderación de clases o métricas específicas orientadas al rendimiento desbalanceado) para evitar interpretaciones erróneas.
- **Binarización de la variable objetivo:** La necesidad de operacionalizar el interés vocacional en una categoría binaria (Sí/No) simplifica la complejidad del proceso de elección del estudiante, excluyendo matices como el interés parcial o la indecisión.

---
                        
## Referencias
* IDES-KNAC (2023). *Actitud de la juventud hacia las profesiones STEM*. Informe preliminar, base de microdatos y diccionario de variables. Disponible en: [Argentina.gob.ar](https://www.argentina.gob.ar/sites/default/files/informe_preliminar_jovenes%20stem.pdf).