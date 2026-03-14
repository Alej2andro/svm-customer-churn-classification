# svm-customer-churn-classification

Clasificación de churn bancario con **Support Vector Machines (kernel RBF)** en R. El proyecto construye un pipeline end-to-end con justificación matemática explícita en cada etapa, desde la exploración de datos hasta la puesta en operación con segmentación accionable de riesgo.

**AUC-ROC: 0.998 · F1-Score: 0.996 · Recall: 0.998**

---

## Pipeline

```
EDA → Encoding → Clean Algorithm → Normalización MinMax → Selección de Variables → SVM RBF → Evaluación → Operación
```

---

## Selección de características — el núcleo del proyecto

El paso más riguroso del pipeline enfrenta **7 selectores** bajo las mismas condiciones: mismo `k`, mismo SVM evaluador (C=1, σ=0.1) y misma validación cruzada estratificada CV-5 sobre Train. Test permanece sellado hasta la evaluación final.

| Selector | Tipo | Criterio |
|---|---|---|
| Fisher J | Ranking univariado | $J_F$ separabilidad por clase |
| SFS | Búsqueda greedy | AUC-SVM incremental |
| Branch & Bound | Búsqueda exhaustiva | Fisher J multivariado + poda |
| Random Forest ★ | Ranking multivariado | Mean Decrease Accuracy |
| Lasso L1 | Regularización | $\|\beta\|_1$ penalización |
| XGBoost | Ranking ensemble | Gain no lineal |
| Mutual Information | Filtro no paramétrico | $I(X;Y)$ |

**Ganador: Random Forest** — AUC CV 0.9994, Score ponderado 0.9979.  
`k_comun = 6` se establece como piso metodológico para garantizar dimensionalidad suficiente ante el dominio absoluto de `complain_1`.

**Variables seleccionadas:** `complain_1`, `age`, `numofproducts`, `balance`, `isactivemember_1`, `creditscore`

---

## Hallazgo principal

`complain_1` concentra prácticamente toda la señal predictiva — hallazgo consistente en los 7 selectores y en cada criterio de importancia (Fisher J, MDA, Gain, $I(X;Y)$, $|\beta|$). El documento concluye con una advertencia operacional crítica: validar la secuencia temporal entre queja y abandono antes de cualquier despliegue productivo.

---

## Resultados

| Métrica | Test |
|---|---|
| AUC-ROC | 0.9980 |
| F1-Score | 0.9961 |
| Recall | 0.9980 |
| Precisión | 0.9942 |
| Kappa | 0.9951 |

**Segmentación de riesgo sobre Test:**
- 🔴 Crítico (>75%): 511 clientes (20.4%)
- 🟢 Bajo (<30%): 1.988 clientes (79.6%)

---

## Stack

```
R · Quarto · tidyverse · e1071 · caret · pROC · ROCR
randomForest · glmnet · xgboost · infotheo · igraph · kableExtra
```

---

## Dataset

[Bank Customer Churn — Kaggle](https://www.kaggle.com/datasets/radheshyamkollipara/bank-customer-churn)  
10.000 clientes · 18 variables · ~20% churn

---

## Publicación

[Ver documento en RPubs](#) 
<https://rpubs.com/Alej5ndro/SVM-clasificacion>

---

*Alejandro Figueroa Rojas · Ingeniero Comercial — Data & Business Intelligence*
