# LOS Prediction for Diabetic ICU Patients — MIMIC-III Clinical Database

## Overview

Diabetic patients undergoing surgery face significantly longer recovery times than non-diabetic patients. Diabetes-related peripheral artery disease reduces blood flow to surgical sites, delaying healing. Poor blood sugar control increases infection risk in surgical wounds. Surgical stress itself raises blood sugar levels, which can trigger ketoacidosis and further complicate recovery. Combined, these factors extend hospital stays and increase costs substantially.

This project builds a machine learning model to predict the **Length of Stay (LOS)** of diabetic patients in the ICU following cardiac surgery — using real, restricted clinical data from the MIMIC-III database.

---

## Clinical Relevance

Accurately forecasting hospital LOS is critical for resource planning across healthcare systems — staffing levels, medication supply, equipment allocation, and bed management. Errors in planning lead to unnecessary costs: overstocked medicines, unused beds, underutilised clinical staff. Beyond cost, poor planning directly reduces the quality of care delivered to patients.

This model aims to support clinical decision-making and hospital supply chain management, improving outcomes for diabetic and non-diabetic patients alike.

---

## Data Access

**Dataset:** MIMIC-III Clinical Database — Medical Information Mart for Intensive Care III (PhysioNet / Beth Israel Deaconess Medical Center / MIT)

Access to this dataset requires:
- A formal PhysioNet data use agreement
- Completion of CITI human subjects research training (MIT-affiliated)

Both requirements were completed prior to data access (CITI certification: February 2022, Score: 94%).

Data was extracted via **Google BigQuery SQL** queries across four MIMIC-III clinical tables:
- `diagnoses_icd` — ICD-9 code 25000 (Diabetes Mellitus) patient cohort
- `admissions` — admission and discharge timestamps, demographics
- `icustays` — ICU care unit and length of stay records
- `patients` — date of birth, gender

---

## Methodology

### 1. Data Extraction
SQL queries via Google BigQuery pulling diabetic patient records across four clinical tables, merged on SUBJECT_ID.

### 2. Feature Engineering
- **LOS Calculation** — difference between admission and discharge timestamps
- **Age Calculation** — derived from date of birth vs. admission time
- **Procedure Count** — number of unique ICD-9 procedures per patient
- **LOS Categorisation** — binary classification: short stay (≤5 days) vs. long stay (>5 days)

### 3. Data Cleaning
- Removed patients with negative LOS (data quality issues)
- Excluded deceased patients (no discharge — LOS not applicable)
- Dropped records with impossible ages (>100 years)
- Imputed missing categorical values (religion, marital status, care unit)

### 4. Model Benchmarking
Eight classification models trained and evaluated on an 67/33 train/test split:

| Model | Notes |
|---|---|
| Decision Tree | Baseline |
| Naive Bayes (Multinomial) | Probabilistic baseline |
| K-Nearest Neighbours (KNN) | Best performing |
| Support Vector Machine (SVM) | |
| Voting Classifier (Ensemble) | Hard voting across all models |
| Bagging Classifier | |
| AdaBoost | |
| Random Forest | |

### 5. Hyperparameter Tuning
GridSearchCV applied to KNN across leaf_size, n_neighbors, and distance metric (p) parameters with 10-fold cross-validation.

**Best model: KNN** with optimised hyperparameters (n_neighbors=4, leaf_size=1, p=1)

---

## Tech Stack

- **Language:** Python
- **Data Access:** Google BigQuery (pandas-gbq)
- **Environment:** Google Colab
- **Libraries:** pandas · NumPy · scikit-learn · matplotlib · seaborn
- **Models:** scikit-learn (DecisionTreeClassifier, MultinomialNB, KNeighborsClassifier, SVC, VotingClassifier, BaggingClassifier, AdaBoostClassifier, RandomForestClassifier)

---

## Repository Structure

```
├── MIMIC_III_PROJECT.ipynb    # Full pipeline: extraction → cleaning → modelling → evaluation
└── README.md
```

---

## Important Note on Data

The MIMIC-III dataset contains sensitive, de-identified patient health information. Raw data is **not included** in this repository. To reproduce this project, you must obtain independent PhysioNet access and CITI certification. The notebook queries data directly from PhysioNet's BigQuery public dataset (`physionet-data.mimiciii_clinical`).

---

## Author

**Tanjil Hasan**
github.com/tanjilh136 · linkedin.com/in/tanjil-hasan-650246169
