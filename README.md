# 💳 FraudScope: Financial Transactions Analytics Dashboard

> End-to-end analytics pipeline detecting fraud patterns, analyzing customer behavior, and uncovering financial trends across **13.3 million transactions** spanning 10 years.

![Power BI](https://img.shields.io/badge/PowerBI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)

---

## 📌 The Problem

Financial institutions process millions of transactions daily. Fraud hides in the noise — at just 0.15%, it seems insignificant until you realize it represents **13,332 real cases** worth millions. Without analytics, risk teams don't know if fraud happens online or in-store, at 3am or noon, at electronics stores or cruise lines. FraudScope solves this.

---

## 🗄️ Dataset

**Source:** Kaggle — Financial Transactions Dataset for Analytics

| File | Size | Contains |
|---|---|---|
| `transactions_data.csv` | 13.3M rows | Date, amount, merchant, card, transaction type |
| `users_data.csv` | 2,000 rows | Age, gender, income, debt, credit score |
| `cards_data.csv` | 6,146 rows | Card brand, type, chip status, credit limit |
| `mcc_codes.json` | 109 codes | Merchant category labels |
| `train_fraud_labels.json` | 8.9M entries | Fraud label (Yes/No) per transaction |

**Coverage:** Jan 2010 – Oct 2019 · 51 US States · 74,831 Merchants · $639M Total Spend

---

## 🏗️ Pipeline

```
Raw CSV/JSON (13.3M rows)
      ↓ Python — Clean & Explore
      ↓ SQL (SQLite) — Aggregate into 8 summary tables
      ↓ Isolation Forest — ML fraud risk scoring
      ↓ Excel — 10-sheet clean export
      ↓ Power BI — 4-page interactive dashboard ✅
```

---

## 🤖 Machine Learning — Why Isolation Forest?

With 99.85% normal vs 0.15% fraud, traditional models just predict "Normal" for everything — catching zero fraud. **Isolation Forest** solves this by learning what normal looks like and flagging anomalies. No balanced classes needed.

| Risk Level | Transactions | Avg Amount | Actual Fraud Rate |
|---|---|---|---|
| 🟢 Low | 432,016 (81%) | $43.70 | 0.138% |
| 🟡 Medium | 97,347 (18%) | $87.90 | 0.183% |
| 🔴 High | 2,874 (0.5%) | $268.57 | 0.424% |

**Validation:** High risk has 3x the actual fraud rate and 6x higher transaction amounts than Low risk — the model genuinely detects anomalies.

---

## 📊 Dashboard — 4 Pages

**Page 1 — Executive KPIs**
13M transactions · $639M spend · $50.56 avg · 0.15% fraud rate · spend growth 2010–2019

**Page 2 — Fraud Analysis**
Top fraud categories · fraud by transaction type · fraud by hour · ML risk distribution

**Page 3 — Customer Segmentation**
Spend by age group · income distribution · credit tier breakdown · fraud by card brand

**Page 4 — Time Trends**
Monthly spend · fraud over time · chip vs swipe vs online adoption · avg transaction by year

---

## 🔑 Key Insights

| # | Insight |
|---|---|
| 🚨 | Cruise Lines have a **38.5% fraud rate** — highest of any category |
| 💻 | Online transactions have **28x more fraud** than swipe |
| 🕙 | Fraud peaks **10am–1pm**, not late night |
| 💸 | High risk transactions average **$268** vs $43 for Low risk |
| 📍 | California leads all states at **$67.6M** spend |
| 👥 | **46–55 age group** is the highest spending demographic |
| 💳 | **$67.5M in refunds** — nearly 10% of total spend |

---

## ▶️ How to Run

```bash
# Install dependencies
pip install pandas numpy scikit-learn openpyxl

# Run pipeline in order
python step1_eda.py        # Clean & explore
python step2_sql.py        # SQL aggregations → Excel
python step3_ml.py         # ML fraud scoring → Excel

# Open Power BI → Get Data → Excel → financial_dashboard_data.xlsx
# Load all 10 sheets → Open FraudScope.pbix
```

---

## 👤 Author

**[Your Name]**
[LinkedIn](https://linkedin.com/in/yourprofile) · [GitHub](https://github.com/yourusername)

⭐ *Star this repo if you found it useful!*
