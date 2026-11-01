 ![Portada del proyecto](../images/portada_tdf_v5.svg)

 # Informe Final

**Título:** Aprendizaje No Supervisado aplicado al consumo de sustancias psicoactivas en jóvenes: un enfoque Nacional con perspectiva territorial en Tierra del Fuego  
**Alumna:** Bárbara Jesabel Rigoni  
**Profesor:** Nicolás Caballero  
**Materia:** Aprendizaje Automático  
**Año:** 2026  

---

## Índice

1. [Contexto y Relevancia del Problema](#1-contexto-y-relevancia-del-problema)
2. [Objetivo General y Específicos](#2-objetivo-general-y-específicos)
3. [Tipo de Problema](#3-tipo-de-problema)
4. [Fuentes de Datos](#4-fuentes-de-datos)
5. [Descripción del Dataset](#5-descripción-del-dataset)
6. [Variables Seleccionadas](#6-variables-seleccionadas)
7. [Preprocesamiento](#7-preprocesamiento)
8. [Análisis Exploratorio de Datos (EDA)](#8-análisis-exploratorio-de-datos-eda)
9. [Modelado — K-Means](#9-modelado--k-means)
10. [Modelado — DBSCAN](#10-modelado--dbscan)
11. [Visualización mediante PCA](#11-visualización-mediante-pca)
12. [Análisis de Outliers](#12-análisis-de-outliers)
13. [Interpretación de Variables](#13-interpretación-de-variables)
14. [Validación mediante Random Forest](#14-validación-mediante-random-forest)
15. [Comparación TDF vs Nacional](#15-comparación-tdf-vs-nacional)
16. [Análisis Comparativo Temporal 2011–2022](#16-análisis-comparativo-temporal-20112022)
17. [Conclusiones Finales](#17-conclusiones-finales)

---

## 1. Contexto y Relevancia del Problema

El consumo de sustancias psicoactivas en adolescentes y jóvenes constituye uno de los principales problemas de salud pública en Argentina. Según la Encuesta Nacional sobre Prevalencias de Consumo de Sustancias Psicoactivas (ENPreCoSP 2011), el 75,7% de los jóvenes de 16 a 24 años consumió alcohol alguna vez en su vida, el 44,8% tabaco y el 10,8% marihuana, configurando un panorama que demanda herramientas de identificación temprana de perfiles de riesgo.

> **Nota conceptual:** Las *sustancias psicoactivas* es un término amplio que incluye toda sustancia que afecta el sistema nervioso central: alcohol, tabaco, drogas ilegales (marihuana, cocaína, pasta base) y también psicofármacos. Los *psicofármacos* son un subgrupo de sustancias psicoactivas: medicamentos de uso psiquiátrico como tranquilizantes y estimulantes, relevados en este dataset cuando fueron consumidos sin indicación médica (`P1A_TR`, `P1A_ES`).

La Provincia de Tierra del Fuego, Antártida e Islas del Atlántico Sur presenta características sociodemográficas particulares —aislamiento geográfico, alta migración interna, elevado costo de vida y escasa oferta de actividades recreativas— que la distinguen del resto del país. Los datos disponibles indican que los jóvenes fueguinos presentan prevalencias de consumo superiores a la media nacional: 82,6% en alcohol (vs. 75,7% nacional), 62,3% en tabaco (vs. 44,8%) y 21,5% en marihuana (vs. 10,8%).

A diferencia de los enfoques tradicionales que imponen categorías de riesgo predefinidas, este proyecto propone aplicar técnicas de Aprendizaje No Supervisado para descubrir, de manera objetiva, los perfiles naturales de consumo presentes en la población joven argentina, permitiendo identificar agrupamientos naturales a partir de las similitudes presentes en los datos, sin sesgos previos.

---

## 2. Objetivo General y Específicos

### Objetivo General

Identificar automáticamente perfiles de consumo de sustancias psicoactivas en jóvenes de entre 16 y 24 años de Argentina, por medio de técnicas de Aprendizaje No Supervisado, y analizar comparativamente los patrones identificados en la Provincia de Tierra del Fuego respecto del resto del país.

### Objetivos Específicos

1. Explorar y preprocesar los datos de la ENPreCoSP 2011, filtrando el grupo etario de 16 a 24 años a nivel nacional y el subconjunto de Tierra del Fuego (`PRVNC = 94`).
2. Aplicar el algoritmo K-Means para descubrir grupos naturales de jóvenes según sus perfiles de consumo y características sociodemográficas, determinando el número óptimo de clusters mediante el método del codo y el coeficiente de Silhouette.
3. Aplicar el algoritmo DBSCAN para validar los grupos encontrados por K-Means y detectar jóvenes con perfiles atípicos (outliers).
4. Aplicar PCA como herramienta de visualización para representar gráficamente la distribución de los jóvenes en un espacio de menor dimensión.
5. Interpretar y caracterizar cada perfil descubierto mediante tres métodos complementarios: comparación de medias, cargas del PCA y dispersión de centroides.
6. Validar la significatividad de los clusters mediante Random Forest.
7. Comparar los perfiles identificados a nivel nacional con los patrones observados en el subconjunto de Tierra del Fuego.
8. Comparar tendencias con los datos de la ENCoPraC 2022 para contextualizar la evolución del consumo entre 2011 y 2022.

---

## 3. Tipo de Problema

El problema se define como un problema de **Aprendizaje No Supervisado**, específicamente de **clustering o agrupamiento**. A diferencia del aprendizaje supervisado, en este enfoque no existe una variable objetivo predefinida. El modelo descubre por sí solo los grupos naturales presentes en los datos.

Este enfoque es adecuado porque:
- Evita el sesgo de imponer categorías de riesgo predefinidas
- Permite descubrir perfiles complejos y multidimensionales
- Facilita la identificación de grupos vulnerables no evidentes
- Genera hipótesis nuevas sobre los factores asociados al consumo

---

## 4. Fuentes de Datos

### Dataset Principal — ENPreCoSP 2011

| Campo | Detalle |
|---|---|
| **Nombre** | Encuesta Nacional sobre Prevalencias de Consumo de Sustancias Psicoactivas |
| **Organismo** | INDEC / Ministerio de Salud / SEDRONAR |
| **Período de relevamiento** | Agosto–octubre de 2011 |
| **Fuente** | https://www.indec.gob.ar/ftp/cuadros/menusuperior/enprecosp/bases_enprecosp2011.rar |
| **Formato** | Texto plano delimitado por pipes (`\|`) |
| **Licencia** | Dominio público |

### Dataset Complementario — ENCoPraC 2022

| Campo | Detalle |
|---|---|
| **Nombre** | Encuesta Nacional sobre Consumos y Prácticas de Cuidado |
| **Organismo** | SEDRONAR / INDEC |
| **Año** | 2022 |
| **Fuente** | https://www.indec.gob.ar/ftp/cuadros/menusuperior/encoprac/base_usuario_encoprac2022.zip |
| **Uso** | Comparación temporal de prevalencias 2011–2022 |

---

## 5. Descripción del Dataset

El análisis se realizó utilizando la Encuesta Nacional sobre Prevalencias de Consumo de Sustancias Psicoactivas (ENPreCoSP 2011). A partir de la base original se seleccionó la población objetivo correspondiente a jóvenes de entre 16 y 24 años y, posteriormente, se identificó el subconjunto perteneciente a la provincia de Tierra del Fuego para realizar la comparación territorial.

### Dimensiones generales

| Característica | Dataset completo | Jóvenes 16-24 años | Subconjunto TDF |
|---|---|---|---|
| Instancias | 34.343 | 6.592 | 265 |
| Variables | 291 | 291 | 291 |
| Codificación | Latin-1 | Latin-1 | Latin-1 |
| Separador | pipe (`\|`) | pipe (`\|`) | pipe (`\|`) |

### Perfil del subconjunto de trabajo

| Variable | Distribución |
|---|---|
| Sexo | Varón: 3.169 (48,1%) \| Mujer: 3.423 (51,9%) |
| Edad promedio | 20,1 años (mín: 16, máx: 24) |
| Alcohol último año | 4.444 consumidores (67,4%) |
| Tabaco último año | 2.139 consumidores (32,4%) |
| Marihuana último año | 283 consumidores (4,3%) |
| Cocaína último año | 61 consumidores (0,9%) |
| Pasta base último año | 4 consumidores (0,1%) |
| Tranquilizantes último año | 74 consumidores (1,1%) |

La población de estudio está compuesta por 6.592 jóvenes de entre 16 y 24 años, con una distribución equilibrada por sexo y una edad promedio de 20,1 años. En términos descriptivos, el alcohol constituye la sustancia de mayor prevalencia, seguido por el tabaco, mientras que el consumo de otras sustancias presenta frecuencias considerablemente menores. Estos resultados ofrecen una primera caracterización de la muestra sobre la cual se desarrollará el análisis de perfiles mediante técnicas de aprendizaje automático.

---

## 6. Variables Seleccionadas

### Variables utilizadas para el modelado

| Variable | Descripción | Categoría |
|---|---|---|
| `BHCH04` | Sexo | Sociodemográfica |
| `BHCH05` | Edad en años cumplidos | Sociodemográfica |
| `NIVINSTR` | Nivel de instrucción | Sociodemográfica |
| `CONDACT` | Condición de actividad | Sociodemográfica |
| `TIPO_H` | Tipo de hogar | Contexto familiar |
| `NBI_TOTAL` | Necesidades Básicas Insatisfechas | Contexto familiar |
| `RANGOING` | Rango de ingreso del hogar | Contexto familiar |
| `BISG01` | Autopercepción de salud general | Salud y entorno |
| `BISG04` | Visita a profesional de salud mental | Salud y entorno |
| `BIAC01` | Conoce consumidores cercanos | Salud y entorno |
| `BIAC03` | Curiosidad por probar drogas | Salud y entorno |
| `BIAC04` | Posibilidad de acceso a drogas | Salud y entorno |
| `REGION` | Región estadística del país | Territorial |
| `POB_URB` | Tamaño del aglomerado urbano | Territorial |
| `PRVNC` | Provincia de residencia | Territorial |

> `PRVNC` fue excluida del modelado y utilizada exclusivamente para la comparación territorial.

La elección de estas variables se fundamenta en la evidencia disponible sobre factores de riesgo asociados al consumo de sustancias psicoactivas, documentados por organismos internacionales y nacionales como MedlinePlus (NIH) y el Observatorio Argentino de Drogas (SEDRONAR). Entre ellos se destacan la influencia del entorno social, el acceso a sustancias, los antecedentes familiares, la salud mental y diversas condiciones socioeconómicas y territoriales.

### Variables de consumo (análisis descriptivo)

| Variable | Descripción |
|---|---|
| `P1A_BA` | Bebidas Alcohólicas |
| `P1A_TA` | Tabaco | 
| `P1A_MA` | Marihuana |
| `P1A_CO` | Cocaína |
| `P1A_PB` | Pasta Base |
| `P1A_TR` | Tranquilizantes |

> Estas variables no fueron utilizadas para construir los clusters, sino para describir e interpretar los perfiles de consumo identificados durante el análisis exploratorio y la comparación entre grupos.

---

# 7. Preprocesamiento

Antes de aplicar los algoritmos de aprendizaje automático, se realizó un proceso de preprocesamiento con el objetivo de garantizar la calidad, consistencia y comparabilidad de los datos. Este procedimiento permitió eliminar inconsistencias, tratar valores faltantes y preparar las variables para el modelado.

| Paso | Acción | Resultado |
|---|---|---|
| 1 | Selección de variables | 16 variables inicialmente seleccionadas |
| 2 | Eliminación de BIAC02 | Variable descartada por presentar aproximadamente un 60% de valores faltantes y aportar información redundante respecto de BIAC01 |
| 3 | Identificación de códigos NS/NC | Se detectaron códigos especiales (9 y 99, según el diccionario de la encuesta) en 5 variables |
| 4 | Reemplazo por valores faltantes (NaN) | Se trataron 567 registros |
| 5 | Imputación | Mediana para `RANGOING` y moda para las restantes variables categóricas |
| 6 | Normalización | Aplicación de `StandardScaler` (media = 0, desvío estándar = 1) |

Cada una de estas etapas tuvo como finalidad minimizar el impacto de errores de registro y asegurar que las diferencias de escala entre variables no condicionaran el funcionamiento de los algoritmos de clustering. En particular, la estandarización fue un paso fundamental, ya que métodos basados en distancia, como K-Means y DBSCAN, son sensibles a la magnitud de las variables y podrían otorgar un peso desproporcionado a aquellas con valores numéricamente mayores.

### Resultado del preprocesamiento

Como resultado de este proceso se obtuvo un conjunto de datos limpio, consistente y sin valores faltantes, apto para la aplicación de los algoritmos de aprendizaje automático. El dataset final utilizado para el modelado quedó conformado por **6.592 registros y 14 variables predictoras**, excluyendo la variable `PRVNC`, que se reservó para la comparación territorial posterior y no se utilizó en la construcción de los clusters.

---

## 8. Análisis Exploratorio de Datos (EDA)

### **Perfil sociodemográfico:**

![Perfil sociodemográfico — Distribución por sexo y edad](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico1_perfil_sociodemografico.png)


La distribución de la muestra presenta un equilibrio entre mujeres (51,9%) y varones (48,1%), mientras que las edades se distribuyen de manera relativamente uniforme entre los 16 y los 24 años. Esta composición resulta favorable para el análisis, ya que reduce el riesgo de que los resultados estén fuertemente condicionados por un predominio de un sexo o de una edad específica dentro de la población estudiada. En consecuencia, las diferencias observadas en etapas posteriores podrán interpretarse con mayor énfasis en función de las variables analizadas y no como un efecto de la estructura demográfica de la muestra.

### **Consumo de sustancias — Nacional vs TDF:**

![Prevalencia de consumo último año — Nacional vs TDF](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico2_consumo_sustancias.png)

| Sustancia | Nacional | TDF | Diferencia |
|---|---|---|---|
| Alcohol | 67,4% | 73,6% | +6,2% |
| Tabaco | 32,4% | 40,0% | +7,6% |
| Marihuana | 4,3% | 6,0% | +1,7% |

El análisis descriptivo muestra que Tierra del Fuego presenta prevalencias de consumo superiores al promedio nacional para las tres sustancias consideradas. Las mayores diferencias se observan en alcohol y tabaco, mientras que el consumo de marihuana también registra valores superiores, aunque con una brecha más moderada.

Estos resultados constituyen uno de los primeros indicios de que la población joven fueguina podría presentar un patrón de consumo diferencial respecto del conjunto nacional. No obstante, esta evidencia corresponde a un análisis descriptivo y será complementada posteriormente mediante técnicas de aprendizaje automático que permitan identificar perfiles de comportamiento más complejos.

### **Factores de entorno — Nacional vs TDF:**

![Factores de entorno social — Nacional vs TDF](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico3_entorno_social.png)

| Factor | Nacional | TDF |
|---|---|---|
| Conoce consumidores | 39,6% | 46,0% |
| Curiosidad por drogas | 14,8% | 25,3% |
| Acceso a drogas | 34,8% | 52,1% |

Las variables relacionadas con el entorno social muestran diferencias relevantes entre Tierra del Fuego y el promedio nacional. En la provincia se observa una mayor proporción de jóvenes que manifiestan conocer personas consumidoras, sentir curiosidad por probar drogas y percibir un acceso más sencillo a estas sustancias.

En conjunto, estos resultados sugieren una mayor exposición al contexto de consumo dentro de la población fueguina. Si bien el análisis descriptivo no permite establecer relaciones causales, estas variables podrían desempeñar un papel importante en la diferenciación de los perfiles identificados durante la etapa de modelado.

### **Perfil educativo y laboral:**

![Perfil educativo y laboral — Nacional vs TDF](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico4_educacion_actividad.png)


En comparación con el promedio nacional, Tierra del Fuego presenta una mayor proporción de jóvenes ocupados y una menor proporción de inactivos. Al mismo tiempo, se observa un abandono educativo más temprano dentro de la muestra provincial.

Estos resultados reflejan una dinámica distinta de inserción educativa y laboral, lo que sugiere que el contexto socioeconómico de la provincia posee características particulares que podrían influir en los patrones de consumo observados. Por este motivo, estas variables serán consideradas en la interpretación integral de los clusters obtenidos.

### **Contexto socioeconómico:**

![Contexto socioeconómico — Nacional vs TDF](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico5_contexto_socioeconomico.png)


El análisis del contexto socioeconómico indica que Tierra del Fuego presenta una menor proporción de hogares con Necesidades Básicas Insatisfechas y una concentración significativamente mayor de jóvenes en los niveles de ingresos más altos respecto del promedio nacional.

Este hallazgo resulta especialmente relevante para la investigación, ya que permite evaluar posteriormente si las diferencias observadas en los perfiles de consumo pueden explicarse únicamente por variables económicas o si intervienen otros factores asociados al entorno social y al acceso a sustancias. En este sentido, el análisis posterior de los modelos permitirá profundizar esta interpretación.

### Hallazgos principales del EDA

| Indicador | Nacional | TDF |
|---|---|---|
| Alcohol último año | 67,4% | 73,6% |
| Tabaco último año | 32,4% | 40,0% |
| Marihuana último año | 4,3% | 6,0% |
| Conoce consumidores | 39,6% | 46,0% |
| Curiosidad por drogas | 14,8% | 25,3% |
| Acceso a drogas | 34,8% | 52,1% |
| Ocupados | 45,1% | 59,6% |
| Ingresos altos | 10,4% | 49,1% |

### Interpretación general del EDA

En conjunto, el análisis exploratorio evidencia que Tierra del Fuego presenta diferencias consistentes respecto del promedio nacional en múltiples dimensiones. La provincia registra mayores prevalencias de consumo de sustancias, una mayor exposición a entornos vinculados al consumo y una percepción de acceso más elevada. Al mismo tiempo, muestra mejores indicadores económicos y una mayor participación laboral juvenil.

La coexistencia de estos resultados sugiere que el fenómeno del consumo no puede interpretarse exclusivamente a partir de variables socioeconómicas tradicionales, sino que responde a una combinación de factores individuales, sociales y contextuales. Estos hallazgos justifican la aplicación de técnicas de aprendizaje automático no supervisado para identificar perfiles de comportamiento más complejos que los observables mediante estadísticas descriptivas.

---

## 9. Modelado — K-Means

### Determinación del K óptimo

![Método del codo y coeficiente de Silhouette](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico7_kmeans_optimo.png)

| K | Inercia | Silhouette |
|---|---|---|
| 2 | 82.400,6 | 0,134 |
| **3** | **76.751,0** | **0,140** ← óptimo |
| 4 | 71.956,2 | 0,099 |
| 5 | 69.028,0 | 0,096 |


Se evaluaron distintas alternativas para la cantidad de clusters con el objetivo de encontrar una partición que combinara calidad estadística e interpretabilidad. Si bien el método del codo y las métricas de evaluación constituyen una guía importante, la decisión final también consideró el significado sustantivo de los grupos obtenidos.

La solución con tres clusters ofreció un equilibrio adecuado entre separación, estabilidad e interpretación, permitiendo distinguir perfiles de bajo, moderado y alto consumo sin generar una segmentación excesivamente fragmentada.

### Perfiles de consumo por cluster

![Perfiles de consumo por cluster — K-Means K=3](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico8_clusters_perfil.png)

### Resultados K-Means (K=3)

| Cluster | Perfil | Jóvenes | % |
|---|---|---|---|
| 🟢 Cluster 2 | Bajo consumo | 3.963 | 60,1% |
| 🔴 Cluster 1 | Alto consumo | 1.989 | 30,2% |
| 🟡 Cluster 0 | Consumo moderado | 640 | 9,7% |

### Métricas

| Métrica | Valor |
|---|---|
| Coeficiente de Silhouette | 0,140 |
| Índice Davies-Bouldin | 2,387 |

El coeficiente de Silhouette obtenido indica una separación moderada entre los clusters. Si bien el valor no es elevado, este comportamiento es esperable en datos provenientes de encuestas sociales, donde los perfiles suelen presentar zonas de superposición y límites poco definidos.

Por su parte, el índice de Davies-Bouldin se encuentra dentro de un rango aceptable para este tipo de aplicaciones, sugiriendo que los grupos identificados poseen una diferenciación suficiente para su interpretación.

### Prevalencia de consumo por cluster

| Sustancia | Bajo consumo | Moderado | Alto consumo |
|---|---|---|---|
| Alcohol | 57,4% | 68,4% | 87,1% |
| Tabaco | 23,1% | 37,8% | 49,4% |
| Marihuana | 0,2% | 6,4% | 11,8% |
| Cocaína | 0,1% | 2,0% | 2,3% |

---

## 10. Modelado — DBSCAN

### Determinación de eps óptimo

![Distancia al 5° vecino más cercano](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico10_dbscan_eps.png)

La selección de los parámetros `eps=2.5` y `min_samples=15` se realizó luego de evaluar distintas combinaciones con el objetivo de obtener una partición estable y comparable con la obtenida mediante K-Means. Esta configuración permitió identificar tres agrupamientos principales manteniendo una adecuada capacidad de separación entre observaciones y, al mismo tiempo, detectar individuos con comportamientos significativamente diferentes del resto de la población.

### Resultados DBSCAN

| Grupo | Jóvenes | % |
|---|---|---|
| Outliers (perfiles atípicos) | 896 | 13,6% |
| Cluster 0 | 4.862 | 73,8% |
| Cluster 1 | 565 | 8,6% |
| Cluster 2 | 269 | 4,1% |

A diferencia de K-Means, DBSCAN no obliga a que todas las observaciones pertenezcan a un cluster. El algoritmo identifica además un conjunto de 896 jóvenes clasificados como outliers o perfiles atípicos, que representan aproximadamente el 13,6% de la muestra.

Desde una perspectiva analítica, este resultado es relevante porque sugiere la existencia de patrones de comportamiento que no se ajustan a los grupos predominantes. En lugar de forzar su asignación a un cluster, el algoritmo los reconoce como casos con características particulares, preservando así una representación más fiel de la estructura de los datos.

### Comparación K-Means vs DBSCAN

| Métrica | K-Means | DBSCAN |
|---|---|---|
| Clusters | 3 | 3 |
| Silhouette | 0,140 | **0,155** ✅ |
| Davies-Bouldin | 2,387 | **1,846** ✅ |
| Outliers detectados | 0 | 896 |

La comparación de métricas muestra que DBSCAN obtuvo un coeficiente de Silhouette ligeramente superior y un índice de Davies-Bouldin inferior al de K-Means, lo que indica una mejor separación y una mayor compacidad de los grupos identificados.

Además, la capacidad de detectar perfiles atípicos constituye una ventaja metodológica importante para este tipo de datos sociales, donde no todos los individuos responden a patrones homogéneos de comportamiento. No obstante, ambos algoritmos identificaron una estructura general consistente de tres perfiles, lo que aporta robustez a los resultados obtenidos y fortalece la interpretación posterior de los clusters.

---

## 11. Visualización mediante PCA

Se aplicó PCA para reducir las 14 variables predictoras a 2 componentes principales y visualizar los clusters en 2D.

### Clusters en espacio PCA

![Visualización de clusters mediante PCA](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico11_pca_clusters.png)

### Clusters sin PCA

![Visualización de clusters sin PCA](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico12_clusters_sin_pca.png)

> El gráfico sin PCA demuestra por qué la reducción de dimensionalidad es necesaria: las variables categóricas forman bandas que dificultan la visualización de los clusters.

| Componente | Varianza explicada |
|---|---|
| PC1 | 14,5% |
| PC2 | 11,4% |
| **Total** | **25,9%** |

La varianza total del 25,9% es esperable en datos de encuestas sociales de alta dimensionalidad, donde los comportamientos humanos no siguen patrones perfectamente separables.
El Análisis de Componentes Principales se utilizó como una herramienta de visualización para representar la estructura de los datos en un espacio bidimensional. Aunque las dos primeras componentes no capturan la totalidad de la variabilidad, permiten observar la existencia de agrupamientos consistentes con los perfiles identificados por el algoritmo de clustering y facilitan su interpretación gráfica.

---

## 12. Análisis de Outliers

DBSCAN identificó **896 jóvenes (13,6%)** con perfiles atípicos.

### Perfil de outliers

![Perfil de outliers vs resto — DBSCAN](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico13_outliers.png)

### Perfil de outliers vs resto

| Sustancia | Outliers | Resto |
|---|---|---|
| Alcohol | 78,3% | 65,7% |
| Tabaco | 48,4% | 29,9% |
| Marihuana | 9,7% | 3,4% |
| Cocaína | 2,3% | 0,7% |

> Los outliers no son jóvenes con perfiles completamente diferentes, sino jóvenes con **consumo más intenso en múltiples sustancias simultáneamente**. Su distribución dispersa en el espacio PCA confirma que son perfiles diversos y no un grupo homogéneo.

**TDF en outliers:** 38 jóvenes (14,3% de los fueguinos son outliers, vs 13,6% nacional).

---

## 13. Interpretación de Variables

Con el objetivo de comprender qué características explican mejor la diferenciación entre los perfiles identificados, se aplicaron tres enfoques complementarios de análisis. En lugar de depender de un único criterio, se compararon las cargas de las componentes principales, la dispersión de los centroides y las diferencias de medias entre clusters. Esta estrategia permitió obtener una visión más robusta sobre la importancia relativa de cada variable.

### Comparación de medias por cluster

![Variables influyentes — Comparación de medias](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico14_variables_influyentes.png)

La comparación de medias evidencia que las diferencias entre los clusters no se distribuyen de manera uniforme entre todas las variables analizadas. Algunas presentan variaciones marcadas, mientras que otras mantienen valores similares entre los grupos, indicando una menor capacidad para discriminar perfiles de consumo.

### Tres métodos comparados

![Importancia de variables — Tres métodos](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico15_importancia_variables.png)

La utilización de tres métodos independientes permitió contrastar la estabilidad de los resultados. A pesar de las diferencias propias de cada técnica, existe una coincidencia significativa en la identificación de las variables asociadas al entorno social como las de mayor capacidad discriminante, lo que incrementa la confianza en la interpretación de los perfiles obtenidos.

### Ranking consolidado

| Variable | Cargas PC1 | Dispersión centroides | Diferencia de medias |
|---|---|---|---|
| 🥇 **Acceso a drogas** | 1° | 2° | 2° |
| 🥈 **Conoce consumidores** | 2° | 3° | 3° |
| 🥉 **Curiosidad drogas** | 3° | 4° | 5° |
| **Ingreso hogar** | 4° | 7° | 1° |
| **Salud mental** | 13° | 1° | 14° |

> Las variables de **entorno social** (acceso a drogas, conocer consumidores, curiosidad) fueron las que mostraron una mayor capacidad para diferenciar los perfiles identificados en el conjunto de datos analizado.

El ranking consolidado muestra una coincidencia notable entre los distintos métodos aplicados. Las variables relacionadas con el acceso a drogas, el conocimiento de personas consumidoras y la curiosidad por probar sustancias aparecen sistemáticamente entre las primeras posiciones, independientemente del criterio utilizado.

Por el contrario, variables tradicionalmente asociadas al contexto socioeconómico, como las Necesidades Básicas Insatisfechas o la composición del hogar, presentan una menor capacidad para diferenciar los perfiles identificados. Si bien el nivel de ingresos del hogar muestra una influencia relevante en algunos análisis, su comportamiento no resulta tan consistente como el observado para las variables de entorno social.

Un resultado particularmente interesante es el caso de la variable vinculada a salud mental, que muestra una alta capacidad discriminante en el análisis de centroides, pero una menor relevancia en los demás métodos. Esta discrepancia sugiere que su influencia podría concentrarse en determinados grupos específicos y no de manera uniforme sobre toda la población analizada.

En conjunto, estos resultados indican que la diferenciación entre los perfiles de consumo parece estar más asociada al contexto social y a la exposición a sustancias que a variables exclusivamente económicas. No obstante, debido al carácter exploratorio del estudio, estas asociaciones deben interpretarse como patrones observados dentro de los datos y no como relaciones causales.

---

## 14. Validación mediante Random Forest

Con el objetivo de evaluar la consistencia de los perfiles obtenidos mediante K-Means, se entrenó un modelo supervisado de Random Forest utilizando los clusters identificados como variable objetivo. Esta estrategia no busca generar un nuevo modelo de clasificación, sino analizar si los grupos descubiertos pueden ser reproducidos a partir de las variables disponibles, aportando una validación adicional de su estructura.

### Matriz de confusión e importancia de variables

![Random Forest — Importancia de variables](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico16_random_forest.png)

### Resultados

| Métrica | Valor |
|---|---|
| **Accuracy** | **98,1%** |
| Precision (macro) | 0,98 |
| Recall (macro) | 0,98 |
| F1-score (macro) | 0,98 |

El modelo obtuvo una exactitud (Accuracy) del 98,1%, acompañada por valores igualmente elevados de precisión, recall y F1-score. En conjunto, estas métricas indican que las variables incluidas en el análisis permiten diferenciar con gran claridad los perfiles identificados por el algoritmo de clustering.

Desde una perspectiva metodológica, este resultado aporta evidencia de que los clusters no representan una partición arbitraria de los datos, sino una estructura consistente que puede ser reconocida por un algoritmo supervisado utilizando la información disponible.

### Importancia de variables (Random Forest)

| Ranking | Variable | Importancia |
|---|---|---|
| 🥇 | Acceso a drogas | ~0,38 |
| 🥈 | Salud mental | ~0,25 |
| 🥉 | Conoce consumidores | ~0,11 |
| 4° | Curiosidad drogas | ~0,06 |
| 5° | Ingreso hogar | ~0,04 |

El análisis de importancia de variables muestra que el acceso a drogas constituye el factor con mayor capacidad para diferenciar los perfiles identificados, seguido por variables relacionadas con la salud mental y el conocimiento de personas consumidoras. También aparecen entre las variables más relevantes la curiosidad por probar drogas y el nivel de ingresos del hogar, aunque con una contribución comparativamente menor.

La coincidencia entre estos resultados y los obtenidos mediante los análisis anteriores fortalece la interpretación de que el entorno social y la exposición a sustancias desempeñan un papel central en la diferenciación de los perfiles observados. En consecuencia, el fenómeno analizado parece responder a una combinación de factores contextuales e individuales más que a una única dimensión socioeconómica.

### Conclusión metodológica

En conjunto, los resultados obtenidos mediante Random Forest respaldan la estabilidad de la segmentación generada por K-Means y muestran que los perfiles identificados pueden distinguirse con un alto grado de precisión a partir de las variables analizadas. Si bien este procedimiento no constituye una demostración de causalidad ni una validación absoluta de los clusters, sí aporta evidencia adicional sobre la coherencia interna de la estructura encontrada.

---

## 15. Comparación TDF vs Nacional

### Distribución de clusters Nacional vs TDF

![Distribución de clusters — Nacional vs TDF](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico9_clusters_tdf.png)

| Indicador | Nacional | Tierra del Fuego |
|---|---|---|
| 🟢 Bajo consumo | 60,1% | 41,1% |
| 🟡 Consumo moderado | 9,7% | 7,5% |
| 🔴 Alto consumo | 30,2% | **51,3%** |

La comparación entre la distribución nacional y la correspondiente a Tierra del Fuego evidencia una diferencia marcada en la composición de los perfiles identificados. Mientras que a nivel nacional la mayor proporción de jóvenes pertenece al perfil de bajo consumo, en Tierra del Fuego aumenta considerablemente la participación del perfil de alto consumo, acompañado por una reducción del grupo de bajo consumo.

Este resultado constituye el principal hallazgo de la investigación, ya que sugiere que la estructura de perfiles obtenida mediante técnicas de aprendizaje automático no se distribuye de manera homogénea en el territorio argentino. En particular, la provincia de Tierra del Fuego presenta una concentración significativamente mayor de jóvenes dentro del perfil de mayor riesgo relativo.

Desde una perspectiva analítica, estos resultados refuerzan la importancia de incorporar el contexto territorial en el estudio del consumo de sustancias. Si bien el presente trabajo tiene un carácter exploratorio y no permite establecer relaciones causales, la magnitud de las diferencias observadas indica que el comportamiento provincial merece un análisis específico y no puede inferirse únicamente a partir de los promedios nacionales.

En términos prácticos, esto implica que mientras aproximadamente tres de cada diez jóvenes integran el perfil de alto consumo en el conjunto nacional, en Tierra del Fuego esa proporción supera los cinco de cada diez. Esta diferencia resume el principal aporte empírico del presente estudio.

---

## 16. Análisis Comparativo Temporal 2011–2022

### Evolución del consumo 2011 vs 2022

![Comparación temporal 2011 vs 2022](https://raw.githubusercontent.com/BarbyJRigoni/Aprendizaje-Automatico-Parcial/main/reports/figures/grafico17_comparacion_temporal.png)

| Sustancia | 2011 | 2022 | Cambio |
|---|---|---|---|
| Alcohol | 67,4% | 70,2% | ↑ +2,8% |
| Tabaco | 32,4% | 22,9% | ↓ -9,5% |
| Marihuana | 4,3% | 17,4% | ↑↑ **+13,1%** |
| Tranquilizantes | 1,1% | 2,7% | ↑ +1,6% |

La comparación entre los datos de 2011 y 2022 muestra que la evolución del consumo no fue homogénea entre las distintas sustancias. Mientras que el consumo de tabaco presenta una disminución importante, el alcohol mantiene una prevalencia elevada con un incremento moderado y la marihuana registra el crecimiento relativo más significativo del período analizado.

Particularmente, el aumento observado en el consumo de marihuana constituye uno de los cambios más relevantes de la serie comparativa, pasando del 4,3% al 17,4%. Este resultado evidencia una modificación sustancial del contexto de consumo entre ambas mediciones y sugiere la necesidad de profundizar futuras investigaciones sobre los factores sociales, culturales y normativos que pudieron intervenir durante ese período.

No obstante, debido a las diferencias metodológicas entre las encuestas utilizadas, estos resultados deben interpretarse como una comparación descriptiva y no como una estimación directa de tendencias causales.

> ### Consideraciones metodológicas

La comparación presentada tiene un carácter exploratorio. La ENCoPraC 2022 no permite realizar una desagregación provincial equivalente a la utilizada en este estudio y únicamente cuatro de las variables predictoras empleadas poseen correspondencia directa entre ambas encuestas. En consecuencia, los resultados deben interpretarse como una referencia descriptiva de la evolución observada y no como una validación del modelo desarrollado.

---

## 17. Conclusiones Finales

El presente estudio permitió identificar perfiles diferenciados de consumo de sustancias psicoactivas en jóvenes argentinos mediante técnicas de Aprendizaje Automático No Supervisado. Tanto K-Means como DBSCAN revelaron una estructura consistente de agrupamientos, lo que indica que la población analizada no presenta un comportamiento homogéneo sino patrones claramente diferenciados de consumo.

Uno de los principales hallazgos de la investigación es que la distribución de estos perfiles difiere entre el conjunto nacional y la provincia de Tierra del Fuego. Mientras que a nivel nacional aproximadamente tres de cada diez jóvenes pertenecen al perfil de alto consumo, en Tierra del Fuego esta proporción supera los cinco de cada diez, constituyendo la diferencia territorial más relevante observada en el estudio.

El análisis de importancia de variables mostró una coincidencia consistente entre distintos métodos de interpretación. Las variables relacionadas con el acceso a sustancias, el conocimiento de personas consumidoras y la curiosidad por probar drogas presentaron una mayor capacidad para diferenciar los perfiles que otros indicadores socioeconómicos tradicionales. Este resultado sugiere que el fenómeno responde a una interacción compleja entre factores sociales e individuales y no exclusivamente a condiciones económicas.

Desde el punto de vista metodológico, el trabajo demuestra el potencial del aprendizaje automático como herramienta exploratoria para el análisis de fenómenos sociales complejos. La combinación de técnicas de clustering, reducción de dimensionalidad y modelos supervisados permitió obtener una interpretación integral de los datos y aportar evidencia complementaria sobre la estabilidad de los perfiles identificados.

## 18. Limitaciones del estudio

Como toda investigación exploratoria basada en técnicas de aprendizaje automático y datos observacionales, el presente trabajo presenta una serie de limitaciones que deben considerarse al interpretar sus resultados.

### Limitaciones metodológicas

El coeficiente de Silhouette obtenido (0,140) refleja una separación moderada entre los clusters identificados. Sin embargo, este comportamiento resulta esperable en estudios sobre conductas humanas, donde los individuos no se distribuyen en categorías perfectamente definidas, sino que presentan zonas de superposición. Como señala Rousseeuw (1987), este tipo de resultados es frecuente en fenómenos sociales complejos y no implica necesariamente una baja utilidad del modelo.

Por otra parte, el algoritmo DBSCAN identificó 896 jóvenes (13,6% de la muestra) como perfiles atípicos. La diversidad de estos casos sugiere que no constituyen un grupo homogéneo, sino un conjunto de comportamientos particulares cuya interpretación requiere análisis específicos que exceden el alcance de este estudio.

### Limitaciones de los datos

El subconjunto correspondiente a Tierra del Fuego está compuesto por 265 registros, por lo que las estimaciones territoriales deben interpretarse con la cautela propia de un tamaño muestral reducido.

Asimismo, la base ENPreCoSP 2011 constituye una fuente de datos con más de una década de antigüedad. Si bien se incorporó una comparación descriptiva con la ENCoPraC 2022 para contextualizar algunos resultados, el modelo desarrollado se construyó sobre información correspondiente a 2011.

### Limitaciones de la comparación temporal

La comparación entre 2011 y 2022 tiene un carácter exclusivamente descriptivo. La ENCoPraC 2022 no dispone de una desagregación provincial equivalente para Tierra del Fuego y solo cuatro de las variables utilizadas en el modelo poseen una correspondencia directa entre ambas encuestas. En consecuencia, esta sección debe interpretarse como una referencia sobre la evolución observada y no como una validación del modelo propuesto.

## 19. Futuras líneas de investigación

### Incorporación de variables de salud mental

La variable de salud mental disponible en la base utilizada mostró una baja capacidad discriminante. Sin embargo, este resultado probablemente refleje las limitaciones del indicador más que la ausencia de relación entre salud mental y consumo. Futuras investigaciones podrían incorporar medidas específicas de ansiedad, depresión, estrés percibido y bienestar psicológico para profundizar este análisis.

### Extensión del análisis a otras provincias patagónicas

Las características observadas en Tierra del Fuego plantean el interés de extender este tipo de análisis a otras provincias de la región patagónica, como Neuquén, Santa Cruz y Chubut. La identificación de patrones similares permitiría evaluar si las diferencias encontradas responden a particularidades locales o a un comportamiento regional más amplio.

### Estudios longitudinales

El presente trabajo posee un diseño transversal, por lo que permite describir la distribución observada en un momento determinado, pero no analizar la evolución individual de los participantes ni establecer relaciones causales. La disponibilidad de estudios longitudinales permitiría comprender con mayor profundidad la dinámica de los perfiles de consumo a lo largo del tiempo.

---

# Aporte del trabajo

El presente trabajo pone de manifiesto el potencial de las técnicas de aprendizaje automático no supervisado para el análisis de fenómenos sociales complejos. A través de la identificación de perfiles de consumo, fue posible reconocer patrones de comportamiento que no resultan evidentes mediante estadísticas descriptivas tradicionales.

Asimismo, la comparación entre Tierra del Fuego y el promedio nacional aporta una perspectiva territorial que destaca la importancia de analizar realidades locales y no únicamente indicadores agregados. Los resultados obtenidos sugieren que la distribución de los perfiles de consumo puede variar significativamente entre regiones, aun cuando compartan un mismo contexto nacional.

Desde el punto de vista metodológico, la investigación integra técnicas de clustering, reducción de dimensionalidad e interpretación de variables en un mismo flujo de análisis, demostrando el valor de combinar herramientas de ciencia de datos con preguntas propias de las ciencias sociales.

En este sentido, el estudio constituye una aproximación exploratoria que puede servir como punto de partida para futuras investigaciones y contribuir al diseño de estrategias de prevención y políticas públicas basadas en evidencia.

---

## Referencias

- INDEC / Ministerio de Salud (2011). *ENPreCoSP 2011 — Encuesta Nacional sobre Prevalencias de Consumo de Sustancias Psicoactivas*. Buenos Aires: INDEC.
- SEDRONAR / INDEC (2022). *ENCoPraC 2022 — Encuesta Nacional sobre Consumos y Prácticas de Cuidado*. Buenos Aires: SEDRONAR.
- MedlinePlus / NIH (2025). *Trastorno de consumo de drogas — Factores de riesgo*. Disponible en: medlineplus.gov
- Rousseeuw, P.J. (1987). *Silhouettes: a graphical aid to the interpretation and validation of cluster analysis*. Journal of Computational and Applied Mathematics, 20, 53–65.
- Observatorio Argentino de Drogas — SEDRONAR. *Informes de prevalencias de consumo en población joven*.

---

*Materia: Aprendizaje Automático | Alumna: Bárbara Jesabel Rigoni | Profesor: Nicolás Caballero | 2026*
