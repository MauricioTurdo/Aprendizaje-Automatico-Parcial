# Informe Metodológico y Análisis Exploratorio de Datos (Entrega 2)

**Autor:** Mauricio Guillermo Turdo  
**Fecha:** 30 de octubre de 2026  

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

- **Fuente y Origen:** El estudio proviene de la base de datos pública generada por el **consorcio IDES-KNAC** en el marco del relevamiento para el MINCyT. El relevamiento de campo se llevó a cabo entre el **17 de julio y el 19 de agosto de 2023**, y el archivo de microdatos fue adquirido y descargado el **15 de octubre de 2026** desde los repositorios de datos abiertos oficiales (`datos.gob.ar`).
- **Dimensiones Brutas:** El archivo original contiene **$2.891$ observaciones y $217$ variables**. La diferencia numérica frente a los $2.880$ registros teóricos reportados en la ficha técnica preliminar **no se encuentra explicada** en la documentación oficial provista por la fuente.
- **Tipos de Datos:** El conjunto comprende variables de naturaleza numérica, ordinal, nominal y binaria, abarcando dimensiones sociodemográficas, trayectorias escolares, rendimiento académico **autopercibido** e intereses profesionales.
- **Condiciones de Uso:** Se trabaja con los microdatos anonimizados publicados por la fuente oficial, cumpliendo con los requerimientos de **reconocimiento de autoría al consorcio IDES-KNAC y comunicación previa por escrito** a los organismos emisores ante la difusión de estudios derivados.

---

## 3. Delimitación Poblacional y Flujo de Procesamiento

El universo bajo estudio abarca a **jóvenes de 16 a 18 años** del ciclo orientado de nivel secundario. 

El flujo de depuración y filtrado poblacional se compone de las siguientes etapas:
1. **Población Bruta Inicial:** $2.891$ registros.
2. **Filtro de Intención Educativa (`P12`):** Se excluyen **$505$ registros** (compuestos por $408$ indecisos de estado escolar, $68$ respuestas negativas y $29$ sin respuesta), reteniendo a **$2.386$ registros** de estudiantes secundarios activos. *(Nota: Se observa un sesgo de exclusión donde el $61,8\%$ de los casos descartados son varones frente al $37,7\%$ de mujeres).*
3. **Filtro de Indecisión Vocacional (`P13_12` - *"No sé aún"*):** Se excluyen **$267$ registros** que manifestaron indecisión absoluta, aislando un conjunto analítico principal de **$2.119$ registros** con orientaciones vocacionales definidas.

---

## 4. Definición de la Variable Objetivo

La variable objetivo ($y$) modela la intención declarada de estudiar una carrera vinculada con Informática, Tecnología, Programación o Ciencia de Datos, derivada de la opción `P13_9 - "Pensás seguir estudiando Informática / Tecnología / Programación / Ciencia de Datos"`:
- **$y = 1$**: El encuestado seleccionó expresamente dicha opción.
- **$y = 0$**: El encuestado no seleccionó dicha opción (elección de otros campos o rumbos alternativos).

---

## 5. Variables Predictoras y Contexto de Relevamiento

El modelo integra un conjunto de **$125$ variables de entrada** que se expanden a **$208$ columnas predictoras finales** tras la codificación *One-Hot*. La información recolectada abarca factores sociodemográficos, recursos tecnológicos, rendimiento autopercibido e intereses vocacionales. 

Para garantizar una matriz libre de fuga de información (*data leakage*), **se excluyeron explícitamente $15$ variables** de la matriz de predictores: los identificadores y ponderadores (`ID`, `POND_SIN_ELEVAR`), el filtro poblacional (`P12`), las opciones simultáneas de intención vocacional (`P13_1` a `P13_8`, `P13_10`, `P13_11`), variables de decisiones posteriores o temporales (`P15`, `P16`, `P18`, `P39`, `P33b`, `P33c`), la variable `P25` por superar el $82\%$ de nulos, y `P21` por colinealidad.  
*Advertencia:* La variable **`P3_d`** presenta una proximidad conceptual muy elevada con el objetivo a predecir, por lo que su peso e impacto serán monitoreados estrictamente en el análisis de importancia de características.

---

## 6. Diagnóstico de Valores Faltantes y Estrategia de Imputación

Del diagnóstico inicial se identificaron 17 variables con valores nulos, destacándose la exclusión formal de **`P25`** por presentar $1.746$ valores faltantes ($82,40\%$).

Para garantizar la integridad y evitar el sesgo por pérdida de información, se definieron estrategias específicas dentro del pipeline:
- **Variables numéricas y ordinales:** Imputación mediante la **mediana**.
- **Variables binarias:** Imputación mediante la categoría más frecuente (**moda**).
- **Variables nominales:** Imputación mediante una **constante fija (`0`)**.
- **Indicadores de códigos especiales:** Creación de *flags* binarias explícitas (`no_tiene_*` y `no_sabe_*`) para capturar patrones de omisión estructural.

---

## 7. Preprocesamiento y Transformación de Características

El preprocesamiento estructurado sobre las variables predictoras de entrada se distribuye de la siguiente forma:
- **Variables ordinales:** Imputación por mediana y estandarización.
- **20 variables nominales:** Imputación por constante y codificación *One-Hot*.
- **Variables numéricas:** Imputación por mediana y estandarización.
- **Variables binarias originales:** Recodificación e imputación por moda.
- **11 indicadores de códigos especiales:** *Flags* binarias para preservar patrones de omisión.

Como resultado de la codificación y transformación, la matriz final quedó conformada por **$208$ columnas predictoras**.

---

## 8. Exportación y Preparación para el Modelado

Los datos procesados se dividieron en conjuntos disjuntos para evitar sesgos:
- **Conjunto de Entrenamiento (`X_train`):** $1.695$ registros.
- **Conjunto de Prueba (`X_test`):** $424$ registros.

Los parámetros de imputación y transformación se calcularon exclusivamente sobre `X_train` y se proyectaron sobre `X_test`.

---

## 9. Limitaciones Metodológicas y Consideraciones Éticas

- **Naturaleza de la Muestra:** Se trata de una muestra obtenida mediante un relevamiento estructurado del consorcio IDES-KNAC, sujeta a las condiciones y limitaciones propias del diseño muestral estratificado por regiones COFECYT.
- **Tratamiento de Exclusiones:** La depuración de los $505$ registros responde al cumplimiento de los criterios de inclusión escolar (`P12`), mientras que la separación de los $267$ casos indecisos evita ruidos de clasificación en la frontera de decisión.
- **Consideraciones Éticas:** El análisis se realiza estrictamente sobre los microdatos anonimizados provistos por la fuente oficial para fines de investigación académica.