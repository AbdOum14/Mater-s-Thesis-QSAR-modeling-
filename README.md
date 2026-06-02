# Modelado QSAR para la predicción de la inhibición de inhibidores no peptídicos de Plasmodium

**Flujo de trabajo quimioinformático con Modelos Predictivos, Dominio de Aplicabilidad e Interpretabilidad para QSAR**

## Overview

Este repositorio contiene la arquitectura computacional completa desarrollada para mi **Trabajo de Fin de Máster (TFM)**. El objetivo principal es el diseño, validación exhaustiva e interpretación post-hoc de modelos predictivos de clasificación basados en relaciones cuantitativas estructura-actividad (**QSAR**).

A partir de datos biológicos crudos eztraidos de la base de datos CHEMBL, la tuberia implementa un riguroso curado químico, el cálculo masivo de descriptores híbridos con Mordred, la optimización simultánea de más de 10 familias de clasificadores, la evaluación geométrica del espacio químico (*Dominio de Aplicabilidad*) y el mapeo de relaciones estructura-actividad (SAR) mediante SHAP.

---

## Flujo de trabajo (Pipeline)

Archivos CSV crudos unificados de ChEMBL
                    |
                    v
     Curado, deduplicación y binarización 
     (Activo: IC50 < 0.05 uM / Inactivo)
                    |
                    v
 Cálculo Quimioinformático de Descriptores
      (Mordred 2D + RDKit Descriptors)
                    |
                    v
     Filtros de características (NaNs)
y reducción de multicolinealidad (R = 0.7-0.9)
                    |
                    v
   Cribado Masivo y Optimización Hyper-Grid
     (>10 Algoritmos en K-Fold Estratificado)
                    |
                    v
   Dominio de Aplicabilidad (Leverage AD)
       (Análisis del Gráfico de Williams)
                    |
                    v
   Reconstrucción y Reentrenamiento Top
          (En entorno aislado SHAP)
                    |
                    v
      Interpretabilidad Local y Global
     (SHAP Summary Plots & Importancias)

---

## Estructura del Repositorio 

En proceso... 🏗️🚧

## Configuración y entornos virtuales

Debido a incompatibilidades estrictas de dependencias heredadas (la libreria `mordred` requiere versiones especificas de la libreria `numpy`) y las versiones modernas necesarias para el cálculo de explicabilidad con `shap`, el flujo de trabajo está diseñado para ejecutarse secuencialmente usando dos entornos virtuales independientes de Conda.

### Entorno 1: `env1` (Requerido para 'workflow.ipynb')
Este entorno ejecuta los pasos del 1 al 7
``` bash
conda create -n tfm python=3.9 -y
conda activate tfm
conda install -c conda-forge rdkit -y
pip install pandas numpy==1.23.5 matplotlib seaborn scipy scikit-learn tqdm xgboost lightgbm mordred notebook
```
### Entorno 2: `env2` (Requerido para 'Interpretabilidad.ipynb')
``` bash
conda create -n tfm_shap python=3.10 -y
conda activate tfm_shap
pip install shap scikit-learn pandas numpy matplotlib seaborn xgboost lightgbm notebook
```
## Flujo de Archivos Intermedios

|Archivo/Carpeta | Origen | Descripción |
|----------------|--------|-------------|
|dataset_unificado.csv | Extracción | Recopilación unificada inicial de datos crudos |
|dataset_final_para_mordred.csv | Paso 1-2 | Datos estandarizados, limpios de duplicados moleculares mediante mediana biológica y binarizados |
|paso4_descriptores_mordred.csv | Paso 4 | Matriz completa de características moleculares bidimensionales |
|paso5_feature_sets.csv | Paso 5 | Resumen estructural de las variables seleccionadas tras los análisis de correlación de Pearson y Spearman |
|paso8_leverage_por_compuesto.csv | Paso 7 | Resultados numéricos detallados del cálculo de la matriz Hat ($h$), umbrales teóricos ($h^*$) y residuos estandarizados ($\sigma$) |
|/resultados_shap/ | Paso 8 | Directorio creado automáticamente para almacenar los gráficos de interpretabilidad local y global |

## Resumen de Metodología 
### Curation Pipeline

- **Normalización**: Unificación de valores de potencia biológica ($IC_{50}$) transformando unidades heterogéneas ($nM$, $\mu M$, $\mu g/mL$) a concentración micromolar ($\mu M$).

- **Filtros estructurales**: Procesamiento molecular basado en RDKit para estandarización y deduplicación basada en la mediana del descriptor de respuesta biológica de compuestos idénticos.

- **Umbral de Actividad**: Clasificación binaria estricta donde una molécula es activa si $IC_{50} < 0.05\,\mu M$ e inactiva en caso contrario.


### Entrenamiento y Evaluación de Modelos

- **Algoritmos Evaluados**: Regresión Logística, Máquinas de Vectores de Soporte (SVM), Vecinos Más Cercanos (KNN), Árboles de Decisión, Bosques Aleatorios (Random Forest), Gradiente Descendiente Estocástico (SGD), AdaBoost, Gradient Boosting, XGBoost, LightGBM y Redes Neuronales Perceptrón Multicapa (MLP)
- **Estrategia de Validación**: Búsqueda en rejilla estructurada (`GridSearchCV`) parametrizada bajo esquemas de validación cruzada estratificada (`Stratified K-Fold`) para mitigar el desbalance de clases.

### Espacio Geométrico y Explicabilidad

- **Dominio de Aplicabilidad**: Detección de extrapolaciones predictivas mediante el método de Leverage. Cálculo del umbral límite $h^* = 3(k+1)/n$ (donde $k$ representa las características seleccionadas y $n$ el número de compuestos de entrenamiento) para estructurar gráficos de Williams.
- **Análisis SAR Post-hoc**: Reconstrucción idéntica del particionado de datos y modelos óptimos para desacoplar el peso e impacto fisicoquímico local y global mediante aproximaciones SHAP (SHapley Additive exPlanations) e importancias por permutación agnósticas.

## Tecnologías y Librerias 

| Componente | Herramientas Utilizadas|
|------------|------------------------|
|Quimioinformática | RDKit, Mordred|
|Modelado y ML | Scikit-Learn, XGBoost, LightGBM|
|Explicabilidad (XAI) | SHAP(TreeExplainer/KernelExplainer)|
|Core Científico | Pandas, Numpy, SciPy, Matplotlib, Seaborn, Tqdm|









