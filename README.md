# healthstat-project-powerbi
Data Analyzing of HealthStat.
# 📊 Telecom Customer Churn & Retention Analysis

An end-to-end Power BI analytics project designed to identify key drivers of customer churn, track retention metrics, and provide actionable recommendations to increase customer lifetime value.

---

## 📸 Executive Dashboard Preview

*(Insert key screenshots of your Power BI report pages here)*

- **Page 1:** Executive Churn Overview & KPI Summary
- **Page 2:** Customer Demographics & Contract Analysis
- **Page 3:** Strategic Risk Factors & Retention Targets

---

## 📌 Executive Summary

This report analyzes overall customer retention dynamics across various demographics, service subscriptions, and billing contracts.

* **Key Objectives:** Identify high-risk churn segments, measure revenue loss, and highlight interventions to improve customer retention.
* **Core Metrics:**
  * **Overall Churn Rate:** Primary KPI for customer attrition over time.
  * **Monthly Recurring Revenue (MRR) Lost:** Total revenue impact from lost accounts.
  * **Customer Tenure Distribution:** Segmenting long-term retention vs. early-stage churn.
  * **Contract Breakdown:** Evaluating churn risk between Month-to-Month contracts and 1-Year/2-Year commitments.

---

## 🛠️ Technical Architecture & Data Modeling

* **Data Modeling:** Structured using a **Star Schema** design with normalized dimension tables (Demographics, Services, Contracts) linked to a central Churn Fact Table.
* **ETL & Data Transformation:** Processed via **Power Query** (data cleaning, removing null values, conditional column creations, and type conversions).
* **Key DAX Calculations:**

```dax
// Total Churn Rate
Churn Rate = 
DIVIDE(
    CALCULATE(COUNT(Fact_Churn[CustomerID]), Fact_Churn[ChurnStatus] = "Yes"),
    COUNT(Fact_Churn[CustomerID]),
    0
)

// Lost Monthly Recurring Revenue (MRR)
Lost MRR = 
CALCULATE(
    SUM(Fact_Churn[MonthlyCharges]), 
    Fact_Churn[ChurnStatus] = "Yes"
)
