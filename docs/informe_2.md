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
                      1. Analizar, depurar y preprocesar los microdatos de la encuesta nacional (IDES-KNAC, 2023), imputando valores faltantes, codificando variables categóricas y depurando registros ambiguos.
                      2. Identificar y seleccionar las variables predictoras clave asociadas con la elección tecnológica, aplicando un control estricto para evitar la fuga de información (*data leakage*).
                      3. Implementar y entrenar algoritmos de clasificación supervisada: **Regresión Logística** (como modelo base de referencia), **Árbol de Decisión** (para la extracción de reglas interpretables) y **Random Forest** (para modelar relaciones complejas y no lineales).
                      4. Evaluar el rendimiento de los modelos mediante métricas de validación (*Accuracy*, *Precision*, *Recall* y *F1-Score*) contrastándolos frente a una línea base sencilla (*baseline* de clase mayoritaria).
                      
                      ---
                        
                        ## 3. Tipo de Problema y Definición de la Variable Objetivo
                        
                        El problema se define como un problema de **Aprendizaje Supervisado de Clasificación Binaria**.
                      
                      * **¿Qué predice exactamente el modelo?**  
                        Predice la probabilidad de que un estudiante manifieste una **intención declarada** de inclinarse por carreras del sector informático y tecnológico al finalizar la secundaria.
                      * **Variable Objetivo ($y$):** Construida a partir de la opción correspondiente en la pregunta **`P13_9`** del estudio IDES-KNAC (2023):
                        * **$y = 1$**: El estudiante selecciona expresamente opciones vinculadas a Informática, Tecnología, Programación o Ciencia de Datos.
                      * **$y = 0$**: El estudiante no selecciona dicha opción.
                      
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
                        
                        ## 7. Modelado Predictivo (Algoritmos Seleccionados)
                        
                        Se implementaron y compararon tres modelos de `scikit-learn`:
                        1. **Logistic Regression:** Modelo lineal de referencia (*baseline*) valorado por su alta interpretabilidad a través de los coeficientes de las variables.
                      2. **Decision Tree Classifier:** Modelo no lineal basado en particiones jerárquicas, ideal para extraer reglas de decisión transparentes sobre los factores actitudinales de los jóvenes.
                      3. **Random Forest Classifier:** Modelo de ensamblaje basado en múltiples árboles de decisión, incorporado para capturar interacciones complejas entre las más de 120 variables predictoras y mitigar el sobreajuste (*overfitting*).
                      
                      ---
                        
                        ## 8. Estrategia de Evaluación y Línea Base (Baseline)
                        
                        * **Partición de Datos:** Se separó el conjunto en entrenamiento (`X_train`) y prueba (`X_test`), asegurando que los transformadores se ajustaran **exclusivamente** sobre el conjunto de entrenamiento para evitar cualquier fuga de información hacia el conjunto de evaluación.
                      * **Métricas de Rendimiento:** Se evaluó mediante *Accuracy*, *Precision*, *Recall* y *F1-Score*, prestando especial atención a las clases desbalanceadas para asegurar la correcta detección de los estudiantes orientados a la tecnología.
                      * **Comparación con Baseline:** Los resultados de los modelos de Machine Learning se contrastaron frente a una referencia sencilla (predecir siempre la clase mayoritaria), demostrando el valor analítico real aportado por los algoritmos frente a una regla constante.
                      
                      ---
                        
                        ## 9. Resultados e Interpretación
                        
                        * Los clasificadores superaron consistentemente la línea base de referencia.
                      * El análisis de importancia de características indicó que las variables vinculadas al entorno social, la autopercepción y la curiosidad hacia la tecnología poseen mayor poder discriminante que las variables puramente socioeconómicas tradicionales.
                      
                      ---
                        
                        ## 10. Conclusiones, Limitaciones y Alcance Válido
                        
                        ### Conclusiones que SÍ se pueden sostener:
                        * Es estadísticamente posible identificar qué factores socioeducativos y actitudinales se asocian de manera más fuerte con la probabilidad de que un joven manifieste interés por la tecnología y la programación en el marco del estudio de 2023.
                      
                      ### Conclusiones que NO se pueden sostener (Limitaciones):
                      * **Causalidad:** Encontrar una asociación estadística entre una actitud favorable y la preferencia tecnológica no demuestra una relación causal directa.
                      * **Conducta Futura:** El modelo predice una *intención declarada* manifestada en el contexto de una encuesta y no una conducta, elección universitaria definitiva o inserción laboral efectiva a largo plazo (lo cual requeriría un estudio longitudinal posterior).
                      * **Extrapolación Temporal y Territorial:** Los patrones hallados reflejan las condiciones del relevamiento de 2023, por lo que no deben trasladarse de manera lineal y automática a otros períodos temporales sin una reevaluación.
                      
                      ---
                        
                        ## Referencias
                        * IDES-KNAC (2023). *Actitud de la juventud hacia las profesiones STEM*. Informe preliminar, base de microdatos y diccionario de variables. Disponible en: [Argentina.gob.ar](https://www.argentina.gob.ar/sites/default/files/informe_preliminar_jovenes%20stem.pdf).