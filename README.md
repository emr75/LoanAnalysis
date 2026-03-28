# Small Business Loan Risk Analysis (2008 Recession)

## Project Overview
This project analyzes **SBA loan data** to predict default likelihood during the 2008 financial crisis. By identifying risk patterns across geography, industry, and financial attributes, the study aims to inform lending decisions during economic downturns.

---

## Core Objectives
* **Predictive Modeling:** Classify loans as "Paid in Full" or "Charged Off" (Default).
* **Risk Identification:** Pinpoint key indicators across different industries and US regions.
* **Comparative Analysis:** Evaluate multiple ML architectures to determine the most robust predictor.

---

## Dataset & Pipeline
**Data Source:** SBA Loan Dataset (Kaggle), filtered for the **2007–2012** recessionary period.

### Data Engineering
* **Cleaning:** Standardized missing values and converted monetary fields to numeric types.
* **Encoding:** * *One-Hot Encoding* for Logistic Regression.
    * *Label Encoding* for tree-based models.
* **Feature Engineering:** Extracted industry categories via **NAICS codes** and derived features like SBA guaranteed payout.
* **Data Split:** 70% Training | 15% Validation | 15% Testing.

---
## LLM-Based Feature Enrichment

To extend the traditional ML pipeline, we introduced an **LLM-based enrichment layer** that transforms each loan record into structured semantic features.

### Approach
Each loan record is converted into a text-based profile and processed using an LLM to extract:

- Industry classification (`industry_label`)
- Business stage (`business_stage`)
- Loan characteristics (`loan_backing_type`)
- Risk factors (`risk_factors`)
- Geographic context (`geo_context`)
- Risk summary (`risk_summary`)
- **Quantitative risk score (`risk_score`, 0–100)**

---
Example output:
```json
{
  "industry_label": "real estate and rental and leasing",
  "business_stage": "new business",
  "risk_factors": ["low documentation loan", "small employee size"],
  "risk_score": 62
}
```
---

### LLM Insights

The LLM-generated risk scores showed directional alignment with actual loan outcomes, with defaulted loans receiving slightly higher average scores.

However, the separation between groups was modest, and the scores were concentrated in a narrow range, indicating that LLM-derived features are more effective as feature enrichment signals rather than standalone predictors.

---

## Model Comparison & Performance

| Model | Key Metrics | Notes |
| :--- | :--- | :--- |
| **Logistic Regression** | ROC-AUC: 0.859 | Served as a strong linear baseline. |
| **Decision Trees** | Variable Depth (1-18) | Optimized for bias/variance tradeoff. |
| **AdaBoost (Final)** | **86% Precision / 83% Recall** | Chosen for superior generalization and lower variance. |

---

## Key Results & Insights

### Geographic Trends
We developed a ZIP-code-level **Recession Resilience Map** for the continental United States.
* **Findings:** Urban areas demonstrated significantly higher resilience than rural regions.
* **Visualization Logic:** Blue indicates high resilience; Red indicates high vulnerability (areas with <20 loans were excluded).

### Industry Resilience
Resilience was calculated as:
$$Resilience = 1 - \text{Average Predicted Default Probability}$$

| **High Resilience Sectors** | **Low Resilience Sectors** |
| :--- | :--- |
| Public Administration | Agriculture & Forestry |
| Real Estate & Leasing | Mining & Oil/Gas Extraction |
| Finance & Insurance | Manufacturing |

--

## LLM Key Takeaways

- Traditional ML models effectively captured structured financial risk patterns.
- LLM-based feature extraction introduced semantic context not present in raw data.
- LLM-derived risk scores showed weak but meaningful alignment with actual defaults.
- Combining structured ML models with LLM-derived features represents a promising hybrid approach for financial risk analysis.

## Project Visuals

### Validation Accuracy by Tree Depth
This plot shows how validation accuracy changed as decision tree depth increased. Performance improved with depth, then began to plateau, which helped guide hyperparameter selection and avoid unnecessary model complexity.

<img src="./images/tree-depth-validation.png" width="50%" alt="Validation Accuracy by Tree Depth">

### Recession Resilience by ZIP Code
This map visualizes resilience across ZIP codes in the continental United States using the score:

`1 - predicted default probability`

Blue indicates more resilient regions, while red indicates less resilient ones.

<img src="./images/zip-resilience-map.png" width="50%" alt="Recession Resilience by ZIP Code">

### Industry Resilience Ranking
This chart ranks industries by their average resilience score, helping compare which sectors appeared stronger or weaker during the recession period.

<img src="./images/industry-resilience.png" width="50%" alt="Industry Resilience Ranking">

### LLM Risk Score by Loan Outcome
This plot shows the distribution of LLM-derived risk scores across loan outcomes. While defaulted loans tend to have slightly higher median risk scores, there is significant overlap between groups, indicating that the LLM signal captures some directional risk but does not strongly separate default from non-default cases.

<img src="./images/boxplot.png" width="50%" alt="boxplot of LLM Risk Score ">

## Tech Stack
* **Language:** Python
* **Data Science:** Pandas, NumPy, Scikit-learn
* **AI Integration:** OpenAI API (LLM feature extraction)
* **Visualization:** Matplotlib, Seaborn
---

**Authors:** Ethan Bell, Elle Robertson, Conor Zhang