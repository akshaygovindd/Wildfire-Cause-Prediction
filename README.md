# Wildfire Cause Prediction

**Course:** INFO 6105 — Data Science Engineering Methods and Tools
**Institution:** Northeastern University, Spring 2026
**Team 6:** Akshay Govind | Varun Singh

---

## What This Is

24 years of US wildfire records. 1.88 million rows. One question: was this fire started by a person or by lightning?

This project builds a binary classifier on the USDA Forest Service fire database (1992-2015). The target is simple — human-caused (1) vs lightning-caused (0) — but the data is not. 83.8% of fires are human-caused, which means accuracy is a useless metric here. A model that predicts "human" every time scores 83.8% and tells you nothing.

We trained five models, focused on F1 score and ROC-AUC, and engineered five features from scratch. The tuned Random Forest hit F1 = 0.9598 on the human-caused class and F1 = 0.78 on the lightning-caused class.

---

## Dataset

- **Source:** USDA Forest Service Fire Occurrence Database
- **Size:** 1.88 million rows, 1992-2015
- **Target:** `STAT_CAUSE_DESCR` — binary encoded as Human (1) vs Lightning/Natural (0)
- **Class split:** 1,435,274 human (83.8%) vs 278,468 natural (16.2%)
- **File size:** ~779 MB — not included in this repo

Dataset download link is in [`dataset_link.txt`](dataset_link.txt).

![Target Distribution](Images/plot_01_target_distribution.png)

---

## Class Imbalance

The 5.2:1 ratio between human and natural fires is the core challenge. Accuracy flatters models that just predict the majority class. We used F1 score as the primary metric because it penalizes both false positives and false negatives, and ROC-AUC to evaluate how well each model separates the two classes across thresholds.

![Class Imbalance](Images/plot_06_class_imbalance.png)

---

## Exploratory Data Analysis

**Geographic distribution** — California, Georgia, and Texas account for the most fires. Lightning-caused fires cluster in the western interior; human-caused fires are concentrated along the coasts and southeast.

![Geographic Analysis](Images/plot_02_geographic_analysis.png)

**Temporal patterns** — Fire frequency peaks in spring (March-April) and summer (July-August). Lightning fires spike in summer months. Human-caused fires are spread more evenly across the year.

![Temporal Analysis](Images/plot_03_temporal_analysis.png)

**Fire size** — Mean fire size is 30.9 acres for human-caused fires and 312.5 acres for natural ones. In the tuned Random Forest, fire size ranks 8th of 12 features by importance (0.049).

![Fire Size Analysis](Images/plot_04_fire_size_analysis.png)

**Correlation heatmap** — Longitude has the strongest correlation with the target (0.34). Latitude is moderately negative (-0.18). Most numeric features are weakly correlated, which is why feature engineering mattered.

![Correlation Heatmap](Images/plot_05_correlation_heatmap.png)

---

## Feature Engineering

12 features total, 5 engineered:

| Feature | Type | Description |
|---|---|---|
| `Season` | Engineered | Spring / Summer / Fall / Winter from discovery month |
| `Is_Drought_Season` | Engineered | Binary flag for Summer and Fall (June-November) |
| `Region` | Engineered | West / Central / East from longitude (below -100, -100 to -85, above -85) |
| `State_Fire_Rate` | Engineered | Each state's share of all fires in the dataset (computed on the full table before the split; it does not use the target) |
| `Owner_Type` | Engineered | Land ownership category (federal, state, private) |
| `longitude` | Raw | Strongest individual predictor |
| `latitude` | Raw | Geographic signal for cause |
| `fire_year` | Raw | Year of occurrence |
| `fire_size` | Raw | Acres burned |
| `response_time` | Raw | Days between discovery and containment (missing values filled with the median) |
| `state` | Raw | US state (label-encoded) |
| `fire_size_class` | Raw | USDA size category A-G |

---

## Models and Results

All models trained on 80/20 train-test split with stratification. KNN was run on a 100K sample due to compute constraints.

| Model | Accuracy | Precision | Recall | F1 Score | ROC-AUC |
|---|---|---|---|---|---|
| Logistic Regression | 0.8087 | 0.9602 | 0.8050 | 0.8758 | 0.8834 |
| SVM (LinearSVC) | 0.8086 | 0.9604 | 0.8046 | 0.8756 | 0.8822 |
| KNN (k=15, 100K sample) | 0.9069 | 0.9319 | 0.9589 | 0.9452 | 0.9215 |
| Decision Tree (max_depth=5) | 0.8575 | 0.9625 | 0.8635 | 0.9103 | 0.9138 |
| Random Forest (baseline) | 0.9319 | 0.9503 | 0.9694 | 0.9597 | 0.9547 |
| **Random Forest (tuned)** | **0.9322** | **0.9529** | **0.9668** | **0.9598** | **0.9579** |

Tuned RF CV mean (5-fold): 0.9590. GridSearchCV improved F1 by +0.0001 over baseline.

The metrics above treat human-caused (1) as the positive class. Per class, the tuned Random Forest gets:

| Class | Precision | Recall | F1 |
|---|---|---|---|
| Human-caused | 0.95 | 0.97 | 0.96 |
| Lightning-caused | 0.82 | 0.75 | 0.78 |

---

## Visualizations

**Logistic Regression**
![Logistic Regression](Images/plot_7_logistic_regression.png)

**SVM (LinearSVC)**
![SVM LinearSVC](Images/plot_8_svm_linearsvc.png)

**KNN (k=15)**
![KNN k=15](Images/plot_9_knn_k=15.png)

**Decision Tree — Confusion Matrix and ROC**
![Decision Tree](Images/plot_10_decision_tree.png)

**Decision Tree — Structure (top 3 levels)**
![Decision Tree Structure](Images/plot_09a_decision_tree.png)

**Random Forest (baseline)**
![Random Forest Baseline](Images/plot_11_random_forest_baseline.png)

**Baseline Model Comparison**
![Baseline Model Comparison](Images/plot_12_baseline_model_comparison.png)

---

## Repo Structure

```
Wildfire-Cause-Prediction/
├── WILDFIRE_FINAL.ipynb                           # Main notebook — all 5 models
├── INFO6105_Team_6_Report.docx                    # Full project report
├── Wildfire-Cause-Prediction-Using-ML-Team6.pptx  # Presentation slides
├── dataset_link.txt                               # Dataset download link
├── Images/                                        # All 13 visualization plots
└── README.md
```

---

## How to Run

1. Download the dataset using the link in `dataset_link.txt`
2. Place the file in the project root
3. Open `WILDFIRE_FINAL.ipynb` in Jupyter Notebook
4. Update the file path in the data loading cell to match your local path
5. Run all cells in order

The notebook requires: `pandas`, `numpy`, `scikit-learn`, `matplotlib`, `seaborn`

---

## Team

| Name | Role |
|---|---|
| Akshay Govind | Feature engineering, model training, EDA, notebook |
| Varun Singh | Data preprocessing, analysis, report |

---

*INFO 6105 — Northeastern University, Spring 2026*
