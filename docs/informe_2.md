# Análisis Predictivo de Intención Vocacional en Tecnología (IDES-KNAC 2023)

Sistema de análisis y modelado predictivo para identificar factores socioeducativos y actitudinales asociados a la elección de carreras en áreas de Tecnología, Programación e Informática en jóvenes argentinos.

---

## Objetivo del Proyecto

El propósito de este trabajo es explorar, preprocesar y construir modelos de clasificación que permitan anticipar la preferencia vocacional por disciplinas tecnológicas a partir de los microdatos del relevamiento IDES-KNAC 2023.

---

## Estructura del Repositorio

El proyecto se organiza bajo la arquitectura estándar modular para garantizar la reproducibilidad y el orden analítico:

```text
├── data/
│   ├── raw/                              <- Datos crudos originales y diccionario de variables
│   │   ├── estudio_stem_usuaria.csv      <- Dataset principal (N = 2.891, 217 variables)
│   │   └── libro_de_variables_usuaria-_stem.xlsx <- Diccionario oficial de variables
│   └── processed/                        <- Datos limpios y preprocesados listos para el modelado
├── cuadernos/                            <- Libretas de Jupyter con análisis exploratorio y preprocesamiento
├── documentos/                           <- Informes metodológicos del proyecto (informe_2.md)
├── modelos/                              <- Archivos binarios de modelos entrenados (.pkl / .joblib)
├── src/                                  <- Scripts auxiliares y funciones reutilizables
├── .gitignore                            <- Filtros para omitir archivos temporales
├── LICENSE                               <- Términos de uso del código (Licencia MIT)
├── README.md                             <- Descripción general del proyecto
└── requirements.txt                      <- Dependencias para replicar el entorno de ejecución