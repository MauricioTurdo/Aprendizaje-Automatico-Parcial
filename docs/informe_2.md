# Informe Metodológico y Análisis Exploratorio de Datos (Entrega 2)

## Índice
1. [Introducción y Objetivos](#1-introducción-y-objetivos)
2. [Descripción del Dataset y Origen](#2-descripción-del-dataset-y-origen)
3. [Delimitación Poblacional y Flujo de Procesamiento](#3-delimitación-poblacional-y-flujo-de-procesamiento)
4. [Definición de la Variable Objetivo](#4-definición-de-la-variable-objetivo)
5. [Variables Predictoras y Contexto de Relevamiento](#5-variables-predictoras-y-contexto-de-relevamiento)
6. [Diagnóstico de Valores Faltantes y Estrategia de Imputación](#6-diagnóstico-de-valores-faltantes-y-estrategia-de-imputación)
7. [Preprocesamiento y Transformación de Características](#7-preprocesamiento-y-transformación-de-características)
8. [Exportación y Preparación para el Modelado](#8-exportación-y-preparación-para-el-modelado)
9. [Limitaciones Metodológicas y Consideraciones Éticas](#9-limitaciones-metodológicas-y-consideraciones-éticas)

---

## 1. Introducción y Objetivos

El presente documento detalla la segunda etapa del proyecto orientada al análisis de las actitudes hacia las carreras científicas y tecnológicas en Argentina, basándose en la base de datos del estudio IDES-KNAC/MINCyT (2023). El objetivo central de esta fase consiste en estructurar, limpiar y transformar el conjunto de datos para construir una matriz predictora robusta y libre de fuga de información (*data leakage*), destinada a modelar la intención de elección vocacional en disciplinas de Informática y Tecnología.

---

## 2. Descripción del Dataset y Origen

* **Fuente y Origen:** El estudio proviene de la base de datos pública generada por el IDES en el marco del relevamiento KNAC para el MINCyT (año 2023). La recolección se efectuó mediante un cuestionario estructurado administrado a través de canales digitales (*online*).
* **Dimensiones Brutas:** El archivo original contiene **$2.891$ observaciones y $217$ variables** (cifra que difiere levemente de los $2.880$ registros teóricos reportados en la ficha técnica preliminar debido a la estructura de exportación del archivo CSV bruto).
* **Tipos de Datos:** El conjunto comprende variables de naturaleza numérica, ordinal, nominal y binaria, abarcando dimensiones sociodemográficas, trayectorias escolares, rendimiento académico autoimpulsado e intereses profesionales.
* **Condiciones de Uso:** Datos de acceso público para fines de investigación académica y desarrollo científico bajo normativas de anonimización.

---

## 3. Delimitación Poblacional y Flujo de Procesamiento

El universo bajo estudio abarca a **jóvenes de 16 a 18 años independientemente de su situación escolar actual** (contemplando tanto a quienes cursan la escuela secundaria como a quienes ya la han finalizado). 

El flujo de depuración y filtrado poblacional se compone de las siguientes etapas:
1. **Población Bruta Inicial:** $2.891$ registros.
2. **Filtro de Intención Educativa (`P12` - *"¿Pensás continuar tus estudios al terminar la escuela?"*):** Se excluyen **$505$ registros** que respondieron de manera negativa o no aplicable, reteniendo a **$2.386$ registros** orientados a continuar estudios superiores.
3. **Filtro de Indecisión Vocacional (`P13_12` - *"No sé aún"*):** Se excluyen **$267$ registros** que manifestaron indecisión absoluta, aislando un conjunto analítico principal de **$2.119$ registros** con orientaciones vocacionales definidas.

---

## 4. Definición de la Variable Objetivo

La variable objetivo ($y$) modela la intención declarada de estudiar una carrera vinculada con Informática, Tecnología, Programación o Ciencia de Datos, derivada de la opción `P13_9 - "Pensás seguir estudiando Informática / Tecnología / Programación / Ciencia de Datos"`:
* **$y = 1$**: El encuestado seleccionó expresamente Informática, Tecnología, Programación o Ciencia de Datos.
* **$y = 0$**: El encuestado no seleccionó dicha opción (lo cual no implica una negativa absoluta hacia la tecnología, sino la elección de otros campos o rumbos alternativos).

Los $267$ casos con respuestas ambiguas o de indecisión absoluta fueron excluidos del subconjunto de entrenamiento y validación para evitar sesgos de estimación.

---

## 5. Variables Predictoras y Contexto de Relevamiento

El modelo integra un conjunto de **$208$ variables predictoras finales** tras el preprocesamiento. La información recolectada abarca un espectro amplio de factores sociodemográficos, disponibilidad de recursos tecnológicos en el hogar, rendimiento académico autopercibido, orientación del bachillerato cursado e intereses vocacionales explícitos (`P3_d`). 

Se destaca que no se excluyeron a priori variables por considerarse metadatos restringidos; cada rasgo incorporado aporta información contextual relevante para el perfilamiento del postulante.

---

## 6. Diagnóstico de Valores Faltantes y Estrategia de Imputación

Del diagnóstico inicial se identificaron 17 variables con valores nulos. Cabe destacar el caso de la variable **`P25`**, la cual presentó $1.746$ valores faltantes ($82,40\%$), motivo por el cual **se excluyó explícitamente** del pipeline.

Para garantizar la integridad y evitar el sesgo por pérdida de información, se definieron estrategias de imputación específicas dentro del pipeline:
* **Variables numéricas y ordinales:** Imputación mediante la **mediana**, seguida de estandarización.
* **Variables binarias:** Imputación mediante la categoría más frecuente (**moda**).
* **Variables nominales:** Imputación mediante una **constante fija (`0`)**, la cual representa metodológicamente la categoría estructural *"no corresponde"*.
* **Indicadores de códigos especiales:** Creación de *flags* binarias explícitas (`no_tiene_*` y `no_sabe_*`) para capturar patrones de omisión estructural o incertidumbre informativa.

---

## 7. Preprocesamiento y Transformación de Características

El preprocesamiento se ejecutó mediante un pipeline estructurado sobre las **$125$ variables predictoras de entrada** (excluyendo `P25` y `P21`), generando el siguiente desglose de transformaciones:
* **31 variables ordinales:** Imputación por mediana y estandarización.
* **21 variables nominales:** Imputación por constante fija (`0`) y codificación *One-Hot*.
* **3 variables numéricas:** Imputación por mediana y estandarización.
* **60 variables binarias originales:** Recodificación e imputación por moda.
* **10 indicadores de códigos especiales:** *Flags* binarias para preservar patrones de omisión.

Como resultado de la codificación *One-Hot* y la integración de transformaciones, la matriz final quedó conformada por **$208$ columnas predictoras**.

---

## 8. Exportación y Preparación para el Modelado

Los datos procesados se dividieron en conjuntos disjuntos para evitar el sesgo de fuga de información (*data leakage*):
* **Conjunto de Entrenamiento (`X_train`):** $1.695$ registros.
* **Conjunto de Prueba (`X_test`):** $424$ registros.

Los parámetros de imputación y transformación se calcularon exclusivamente sobre `X_train` y se proyectaron idénticamente sobre `X_test`. Finalmente, la matriz depurada se exportó bajo el archivo `dataset_filtrado_y_metadatos.csv`.

---

## 9. Limitaciones Metodológicas y Consideraciones Éticas

* **Naturaleza de la Muestra:** Se trata de una **muestra no probabilística por cuotas relevada mediante un canal online**, lo cual restringe la inferencia estadística estricta hacia la totalidad de la población nacional de jóvenes.
* **Tratamiento de Exclusiones:** La exclusión de los  $505$  registros responde al diseño condicional del instrumento (`P12`)- ¿Pensás continuar tus estudios al terminar la escuela?, mientras que la separación de los $267$ casos indecisos evita ruidos de clasificación en la frontera de decisión.
* **Consideraciones Éticas:** El tratamiento de los microdatos cumple con los estándares de anonimización y preservación de la privacidad establecidos para la investigación social y educativa.