# Telecom Customer Churn Analysis and Prediction

## 📌 Overview
Customer churn is a major challenge in the telecommunications industry, where losing customers directly impacts revenue and increases acquisition costs. This project analyzes telecom customer data to identify churn patterns, understand behavioral drivers, and build a predictive model to flag at-risk customers before they leave — supporting data-driven retention strategies.

## ❓ Problem Statement
Customer churn results in significant revenue loss and increased customer acquisition costs. Businesses need a systematic way to analyze customer data, identify high-risk segments, understand the factors contributing to churn, and forecast future attrition so retention efforts can be targeted proactively rather than reactively.

## 🎯 Objectives
- Analyze customer demographics, geography, account details and service usage
- Identify key churn drivers through exploratory analysis
- Build a reproducible data pipeline from raw data to cleaned, model-ready data
- Train and evaluate a predictive model, tuned deliberately for business relevance
- Visualize churn trends and predictions in an interactive dashboard

## 🗂️ Dataset
- 6,418 customer records
- 32 features covering demographics, account details, subscribed services, and billing
- Customers fall into three groups: **Stayed** (4,275), **Churned** (1,732), **Joined** (411 — new customers with unknown future outcome, used as the prediction target group)
## 🛠️ Tools Used
- **SQL (SQLite)** — data cleaning, quality checks, and view creation, executed as a live part of the pipeline (originally prototyped in SQL Server, adapted to SQLite for full reproducibility without requiring external database setup)
- **Python (pandas, scikit-learn)** — encoding, model training, evaluation, and threshold tuning
- **Power BI** — interactive dashboard for churn trends and prediction results
## 🔄 Pipeline / Approach
1. **Data cleaning (SQL):** Audited nulls across all columns, filled with sensible defaults, and fixed a data quality issue (negative `Monthly_Charge` values).
2. **Train/predict split (SQL views):** `vw_ChurnData` (known outcomes, for training) vs. `vw_JoinData` (new customers, for prediction).
3. **Feature encoding (Python):** Label encoding for binary features, one-hot encoding for multi-category nominal features (avoids implying false order).
4. **Model training:** Random Forest with `class_weight='balanced'` to handle class imbalance.
5. **Threshold tuning:** Adjusted the classification threshold to 0.35 (from default 0.5) to prioritize recall — catching more at-risk customers matters more than avoiding false alarms in a retention context.
6. **Leakage check:** Verified cumulative features (`Total_Revenue`, `Total_Charges`) weren't inflating results — removing them changed F1-score by under 2%.
7. **Prediction:** Applied the tuned model to the 411 "Joined" customers, generating probability-ranked churn risk scores.
## 📊 Results
 
**Model performance (Churned class, at tuned threshold of 0.35):**
 
| Metric | Score |
|---|---|
| Precision | 0.70 |
| Recall | 0.76 |
| F1-score | 0.73 |
| ROC-AUC | 0.891 |
| Overall Accuracy | 0.84 |
 
**Prediction output:** 393 of 411 "Joined" customers flagged as high churn risk, each with an individual churn probability score for prioritization.
 
## 💡 Key Insights
- **Contract type is the strongest predictor** — Month-to-Month customers churn far more than One/Two Year contract holders.
- **Early tenure is high-risk** — churn is concentrated in the first 0–24 months, then drops sharply.
- **Age 35+ customers** make up a larger share of predicted churners — retention messaging may need to be age-tailored.
- **Geographic concentration** — Uttar Pradesh, Maharashtra, Tamil Nadu, Karnataka, and Andhra Pradesh have the highest predicted churn volumes.
- **No leakage detected** — Total_Revenue/Total_Charges rank high in importance but aren't inflating results; real signal comes from contract, tenure, and service usage.
## ✅ Recommendations
- Prioritize retention offers and proactive outreach for Month-to-Month customers, especially within their first 24 months of tenure.
- Encourage contract upgrades through loyalty discounts or bundled service incentives to shift customers away from high-risk Month-to-Month plans.
- Tailor engagement and support programs for customers aged 35+ to improve long-term retention.
- Focus regional retention campaigns and service quality investment in the highest-risk states identified above.
- Use the model's probability scores (not just the binary flag) to prioritize outreach — customers with the highest churn probability should be contacted first, given limited retention resources.
## ⚠️ Limitations
- The model was trained on a snapshot of historical data; churn drivers may shift over time and the model would benefit from periodic retraining.
- Threshold selection (0.35) reflects a business assumption that recall matters more than precision in this context — a different business priority would warrant a different threshold.
- Random Forest's `class_weight='balanced'` had limited effect on recall in practice; threshold tuning was the more effective lever here, which is a useful finding for similar imbalanced classification problems.


## 📁 Project Structure
```
├── churn_analysis.sql          # SQL data quality checks, cleaning, and view creation
├── Telecom_data.ipynb          # Full pipeline: SQLite setup, encoding, modeling, evaluation, prediction
├── Customer_Data.csv           # Raw dataset
├── Dashboard/                  # Power BI dashboard files and screenshots
└── README.md
```
 
## 🧩 Notes on Tooling
The SQL scripts in this repo were originally written and prototyped in SQL Server (T-SQL syntax). For full reproducibility — so anyone can clone this repo and run the complete pipeline without needing a SQL Server instance — the queries were adapted to SQLite syntax (e.g., `ISNULL` → `COALESCE`) and executed directly within the Python notebook using a local, file-based SQLite database.
