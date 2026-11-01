# 📊 Análisis Predictivo de Intención Vocacional en Tecnología (IDES-KNAC 2023)

Sistema de análisis y modelado predictivo para identificar factores socioeducativos y actitudinales asociados a la intención de estudio en áreas de Tecnología, Programación e Informática en jóvenes argentinos.

---

## 🎯 Objetivo del Proyecto

El propósito de este trabajo es explorar, preprocesar y construir modelos de clasificación que permitan anticipar la preferencia vocacional por disciplinas tecnológicas a partir de microdatos del relevamiento 2023.

---

## 📁 Estructura del Repositorio

El proyecto se organiza de forma modular para garantizar la reproducibilidad y el orden analítico:

```text
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