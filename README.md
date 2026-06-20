# Glucose Spike Prediction

### Predictive Analytics and AI-Driven Nutrition Solution for Early Detection and Management of Type 2 Diabetes

!\[Python](https://img.shields.io/badge/Python-3.10-blue)
!\[Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.4-orange)
!\[XGBoost](https://img.shields.io/badge/XGBoost-2.0-green)
!\[Status](https://img.shields.io/badge/Status-Complete-brightgreen)

\---

## Background

Type 2 Diabetes (T2D) affects over 400 million people worldwide and is strongly
linked to dietary behaviour. A glucose spike is a rapid rise in blood sugar
exceeding safe thresholds after eating, which is one of the most dangerous
short-term events for diabetic patients, increasing the risk of cardiovascular
complications, neuropathy, and long-term organ damage. Early and accurate
prediction of glucose spikes based on a patient's meal composition and lifestyle
factors could enable real-time dietary intervention and personalised nutrition
planning.

This project builds a machine learning pipeline that predicts whether a given
meal will cause a dangerous glucose spike, based on the patient's nutritional
intake and lifestyle factors. The goal is to enable real-time dietary intervention
and personalised nutrition planning through an AI-driven recommendation system.

\---

## Dataset

The **Glucose Spike Dataset** contains **5,150 patient meal records** across **28 features**, covering:

* **Demographics** — age, gender, BMI, diabetes type
* **Meal composition** — carbohydrate, protein, fat, fiber, and sugar intake; glycaemic index; portion size
* **Lifestyle factors** — physical activity, stress level, sleep hours, smoking status, alcohol consumption
* **Clinical management** — insulin dose, medication adherence
* **Glucose outcomes** — pre-meal glucose, post-meal glucose, glucose change
* **Target variable** — `glucose\_spike` (1 = dangerous post-meal glucose spike, 0 = no spike)

>  The raw dataset is not included in this repository. Place your copy at:
> `data/raw/Glucose\_Spike\_Dataset - Sheet1.csv`

\---

## Project Pipeline

The project is split across **5 notebooks**. Run them in order, each one saves
its output for the next to pick up.

|#|Notebook|Stage|Output|
|-|-|-|-|
|01|`01\_data\_cleaning.ipynb`|Load raw data, explore, remove duplicates, impute missing values, cap outliers|`data/processed/glucose\_spike\_cleaned.csv`|
|02|`02\_eda.ipynb`|Univariate analysis, correlation heatmap, group-based spike rate analysis|Charts|
|03|`03\_feature\_engineering.ipynb`|21 new features - BMI/age segmentation, interaction terms, macronutrient ratios, lifestyle risk score, etc|`data/processed/glucose\_spike\_features.csv`|
|04|`04\_model\_development.ipynb`|Encode, split, scale, SMOTE, train Logistic Regression / Random Forest / XGBoost|`models/\*.pkl`|
|05|`05\_model\_evaluation.ipynb`|Compare models, ROC curves, GridSearchCV, select and save best model|`models/glucose\_spike\_model.pkl`|

\---

## Results

All three models were trained on SMOTE-resampled data and evaluated on an unseen test set.

|Model|Accuracy|Precision|Recall|F1-Score|ROC-AUC|
|-|-|-|-|-|-|
|Logistic Regression|0.770|0.7316|0.7948|0.7619|**0.8557** |
|Random Forest|0.765|0.7317|0.7775|0.7539|0.8379|
|XGBoost|0.760|0.7290|0.7667|0.7474|0.8386|

### Key Finding

> Logistic Regression achieved the highest ROC-AUC of \*\*0.8557\*\*, which may be
> attributed to the linear nature of the engineered features, the small-to-medium
> dataset size (\~5,000 records), and the synthetic samples produced by SMOTE which
> favour linear decision boundaries. All three models exceeded the \*\*0.80 AUC
> threshold\*\* considered acceptable for clinical AI applications.

\---

## Feature Engineering Summary

21 new features were created in `03\_feature\_engineering.ipynb`:

|Category|Features Created|
|-|-|
|Segmentation|`bmi\_category`, `age\_group`|
|Interaction Features|`gi\_portion`, `carb\_gi`, `activity\_bmi`, `stress\_sleep`, `sugar\_gl`, `risk\_adherence`, `insulin\_carb`, `premeal\_carb`|
|Macronutrient Ratios|`protein\_carb\_ratio`, `fiber\_carb\_ratio`, `sugar\_carb\_ratio`, `fat\_carb\_ratio`, `carb\_macro\_pct`, `protein\_macro\_pct`, `fat\_macro\_pct`|
|Lifestyle Risk|`lifestyle\_risk\_score`, `high\_lifestyle\_risk`, `smokes`, `drinks`|

\---

SHAP Explainability
Model predictions are explained using SHAP (SHapley Additive exPlanations) in
`06_shap_explainability.ipynb`. In a healthcare AI system, a prediction alone is
not enough — clinicians and patients need to understand why the model flagged a
meal as high-risk.
What SHAP Provides
Plot	What it Shows
Bar Plot	Overall feature importance ranking across all patients
Beeswarm Plot	Importance + direction of impact + distribution across all patients
Dependence Plots	How each top feature's value maps to its spike risk contribution
Waterfall Plot (Spike)	Step-by-step explanation of why a specific high-risk patient was flagged
Waterfall Plot (No Spike)	Step-by-step explanation of why a low-risk patient was cleared
Force Plot	Interactive horizontal explanation for individual predictions
Key Clinical Finding from SHAP
The SHAP analysis confirmed that the dietary features engineered in notebook 03
are the dominant drivers of glucose spike prediction:
`carb_intake` and `premeal_carb` — highest positive impact on spike risk
`sugar_carb_ratio` and `gi_portion` — strong spike risk amplifiers
`fiber_carb_ratio` — strongest protective feature (high fiber reduces spike risk)
`insulin_carb` — higher insulin coverage relative to carb load reduces risk
`lifestyle_risk_score` — aggregate lifestyle burden acts as a secondary risk factor
> This validates the clinical relevance of the feature engineering pipeline and
> provides actionable dietary insight: reducing carbohydrate and sugar intake while
> increasing fibre are the most impactful interventions for spike prevention.
---

## Setup \& Installation

### 1\. Clone the Repository

```bash
git clone https://github.com/DataRevivalist/glucose-spike-prediction.git
cd glucose-spike-prediction
```

### 2\. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3\. Add the Dataset

Place your CSV file at:

```
data/raw/Glucose\_Spike\_Dataset - Sheet1.csv
```

### 4\. Run the Notebooks in Order

```bash
jupyter notebook
```

Then open and run each notebook from **01 through 05**.

> The `data/processed/` and `models/` folders are created automatically
> by the notebooks, you do not need to create them manually.

\---

## Project Structure

```
glucose-spike-prediction/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── raw/                              - place original CSV here (not tracked by Git)
│   └── processed/                        - auto-generated by notebooks
│       ├── glucose\_spike\_cleaned.csv
│       └── glucose\_spike\_features.csv
│
├── models/                               - auto-generated by notebooks
│   ├── glucose\_spike\_model.pkl           - final best tuned model
│   └── glucose\_spike\_scaler.pkl          - fitted StandardScaler
│
├── notebooks/
│   ├── 01\_data\_cleaning.ipynb
│   ├── 02\_eda.ipynb
│   ├── 03\_feature\_engineering.ipynb
│   ├── 04\_model\_development.ipynb
│   ├── 05\_model\_evaluation.ipynb
│	└── 06_shap_explainability.ipynb

└── outputs/
    └── figures/                          - saved charts and visualisations
```

\---

## Tools \& Libraries

|Tool|Version|Purpose|
|-|-|-|
|Python|3.10|Core language|
|Pandas|2.0+|Data manipulation|
|NumPy|1.26+|Numerical computing|
|Matplotlib|3.8+|Visualisation|
|Seaborn|0.13+|Statistical visualisation|
|Scikit-learn|1.4+|Preprocessing, modelling, evaluation|
|XGBoost|2.0+|Gradient boosting classifier|
|Imbalanced-learn|0.12+|SMOTE oversampling|
|Joblib|1.3+|Model serialisation|
|Jupyter|1.0+|Notebook environment|

\---

## Version History

|Version|Tag|Description|
|-|-|-|
|1.0|`v1.0`|Baseline pipeline complete - data cleaning, EDA, feature engineering, three trained models, hyperparameter tuning|

\---

## Author

**Ifeoluwa Adebiyi**

\---

## License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT),
free to use for educational and research purposes with attribution.

