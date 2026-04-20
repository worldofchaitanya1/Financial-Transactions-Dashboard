# 💳 FraudScope: Financial Transactions Analytics Dashboard

> An end-to-end analytics pipeline that detects fraud patterns, segments customers, and uncovers financial trends across **13.3 million real-world transactions** spanning 10 years — powered by Python, SQL, Machine Learning and Power BI.

![Power BI](https://img.shields.io/badge/PowerBI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)

---

## 📌 Project Overview

Financial institutions process millions of transactions every day. Fraud hides in the noise — at just 0.15% it seems insignificant, until you realize it represents **13,332 real cases** worth millions of dollars. Without a proper analytics system, risk teams have no way of knowing:

- Is fraud happening more online or in-store?
- Which merchant categories are the biggest targets?
- Does fraud happen at 3am or during business hours?
- Which customers are most valuable and most at risk?

**FraudScope** answers all of these questions. It takes 13.3 million raw, messy transactions and transforms them into a clean, interactive 4-page Power BI dashboard — combining traditional SQL aggregations with Machine Learning-powered fraud scoring to deliver insights that are actually actionable.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| 🐍 Python (pandas, scikit-learn, openpyxl) | Data cleaning, EDA, feature engineering, ML model |
| 🗄️ SQL (SQLite) | Fast aggregation queries on 13.3M rows |
| 🤖 Isolation Forest (scikit-learn) | Unsupervised ML for fraud risk scoring |
| 📊 Power BI Desktop | Interactive 4-page dashboard |
| 📁 Microsoft Excel (.xlsx) | Clean data bridge between Python and Power BI |
| 📂 JSON | Source format for fraud labels and MCC merchant codes |

---

## 📦 Dataset

**Source:** Kaggle — Financial Transactions Dataset for Analytics

| File | Size | Description |
|---|---|---|
| `transactions_data.csv` | 13.3M rows | Core fact table — date, amount, merchant, card, transaction type |
| `users_data.csv` | 2,000 rows | Customer demographics — age, gender, income, debt, credit score |
| `cards_data.csv` | 6,146 rows | Card details — brand, type, chip status, credit limit |
| `mcc_codes.json` | 109 codes | Merchant Category Code labels |
| `train_fraud_labels.json` | 8.9M entries | Binary fraud label (Yes/No) per transaction |

**Coverage:** January 2010 – October 2019 · 51 US States · 74,831 Merchants · 1,219 Active Customers · **$639M Total Spend**

---

## 🏗️ Project Architecture

```
Raw CSV/JSON files (13.3M rows, messy)
        ↓
Step 1 — Python: Clean, explore, fix data types, join fraud labels
        ↓
Step 2 — SQL (SQLite): Aggregate 13.3M rows into 8 summary tables
        ↓
Step 3 — Python ML: Train Isolation Forest, score every transaction
        ↓
Step 4 — Excel: Export 10 clean sheets as Power BI data source
        ↓
Step 5 — Power BI: Build 4-page interactive dashboard ✅
```

---

## 🧹 Data Preprocessing

The raw dataset had several issues that needed fixing before any analysis:

| Problem | Fix Applied |
|---|---|
| `amount` stored as string (`"$14.57"`) | Stripped `$` sign, converted to float |
| `date` stored as plain string | Parsed to datetime, extracted year / month / hour |
| `merchant_state` had 1.5M nulls | Online transactions — expected, kept as-is |
| `yearly_income`, `total_debt` were strings | Stripped `$` and commas, converted to float |
| Fraud labels in a separate JSON file | Joined onto transactions using transaction ID |
| 199 unique "states" in data | Filtered to 51 valid US states only |
| 13.3M rows causing RAM crashes | Chunk processing — 500K rows at a time |

**Feature Engineering added:**
- `year`, `month`, `hour` extracted from datetime
- `is_refund` flag for negative amount transactions
- `is_night` flag for transactions between midnight and 5am
- Users bucketed into `age_group`, `income_group`, `credit_tier`
- MCC codes mapped to human-readable merchant categories

---

## 🤖 Machine Learning — Isolation Forest

### Why not a regular ML model?

The dataset has extreme class imbalance:
```
Normal transactions:  99.85%
Fraudulent:            0.15%
```

A standard model like Logistic Regression or Random Forest would simply predict "Normal" for everything and achieve 99.85% accuracy — while catching zero actual fraud. This is the **accuracy paradox**.

**Isolation Forest** was specifically designed for this scenario. Instead of learning what fraud looks like, it learns what normal looks like — and flags anything that doesn't fit. It builds 200 random decision trees and measures how many splits it takes to isolate each transaction. Fraudulent transactions are unusual, so they get isolated in very few splits. Normal transactions take many more splits to isolate.

### Model Setup
```python
IsolationForest(
    n_estimators=200,     # 200 trees for stable, reliable scoring
    contamination=0.015,  # expected anomaly rate matching our 0.15% fraud rate
    random_state=42,
    n_jobs=-1             # use all available CPU cores
)
```

### Results

| Risk Level | Transactions | Avg Transaction | Actual Fraud Rate |
|---|---|---|---|
| 🟢 Low | 432,016 (81%) | $43.70 | 0.138% |
| 🟡 Medium | 97,347 (18%) | $87.90 | 0.183% |
| 🔴 High | 2,874 (0.5%) | $268.57 | 0.424% |

**The model works.** High risk transactions have 3x the actual fraud rate of Low risk, and their average transaction amount is 6x higher. Fraudsters spend significantly more per transaction — and the Isolation Forest caught that pattern entirely on its own without being told what fraud looks like.

---

## 📊 Dashboard Walkthrough

### Page 1 — Executive KPIs
*For: C-suite executives, managers, anyone needing the headline story fast*

- **6 KPI Cards** — Total Transactions (13M), Total Spend ($639M), Avg Transaction ($50.56), Total Refunds (660K), Fraud Rate (0.15%), Unique Customers (1,219)
- **Total Spend by Year** — Bar chart showing steady growth from ~$50M in 2010 to ~$65M by 2018
- **Transactions by Type** — Donut showing Swipe 52%, Chip 36%, Online 12%
- **Total Fraud Cases** — Bold red KPI card showing 13,332 fraud cases

---

### Page 2 — Fraud Analysis
*For: Risk teams, fraud analysts, compliance officers*

- **Top 10 Fraud Categories** — Cruise Lines lead at 38.5%, Music Stores at 23.8%. High-value hard-to-reverse purchases are primary fraud targets
- **Fraud by Transaction Type** — Online transactions have 28x higher fraud rate than Swipe — a clear case for stronger online authentication
- **Fraud by Hour of Day** — Fraud peaks 10am–1pm, not late night. Fraudsters operate during busy business hours when alerts are easier to miss
- **ML Risk Distribution** — 81% Low, 18% Medium, 0.5% High. The High risk bucket is where fraud teams should focus daily attention

---

### Page 3 — Customer Segmentation
*For: Marketing teams, product managers, customer success*

- **Spend by Age Group** — The 46–55 age group is the highest spending and most valuable customer demographic
- **Transactions by Income Group** — The 30–60K bracket drives the majority of transaction volume with 1,324 out of 2,000 users
- **Transactions by Credit Tier** — Good credit score users are the largest active group, revealing a financially stable core customer base
- **Fraud Rate by Card Brand** — Reveals which card networks carry the highest fraud exposure for risk-based authentication decisions

---

### Page 4 — Time Trends
*For: Strategy teams, analysts, business forecasters*

- **Monthly Spend Over Time** — 10-year spend trajectory with seasonal patterns across 118 monthly data points
- **Monthly Fraud Cases Over Time** — Fraud volume across the decade with spikes correlating to major industry data breaches
- **Transaction Types Over Time** — Dramatic rise of Chip transactions post-2015 following the EMV mandate, while Swipe steadily declines
- **Avg Transaction Value by Year** — Tracks whether customers spend more or less per transaction over time, revealing purchasing behavior shifts

---

## 🔑 Key Insights

| Insight | Finding |
|---|---|
| 🚨 Highest fraud category | Cruise Lines at **38.5% fraud rate** |
| 💻 Riskiest transaction type | Online = **28x more fraud** than swipe |
| 🕙 Peak fraud time | **10am – 1pm**, not late night |
| 💸 Fraud transaction size | High risk averages **$268** vs $43 for Low risk |
| 📍 Top spending state | California at **$67.6M** total spend |
| 👥 Highest spending demographic | **46–55 age group** |
| 💳 Refund leakage | **$67.5M in refunds** = ~10% of total spend |
| 📈 Revenue growth | Spend grew from **$50M → $65M** over the decade |
| 🤖 ML validation | High risk bucket has **3x actual fraud rate** of Low risk |

---

## 💼 Business Impact

**Fraud Prevention** — Risk teams can now pinpoint exactly where to focus. Cruise Lines, Music Stores and Electronics account for a disproportionate share of fraud. Online fraud at 28x the in-store rate makes a clear case for mandatory two-factor authentication on all card-not-present transactions.

**Operational Efficiency** — Knowing fraud peaks 10am–1pm allows fraud operations teams to schedule highest-capacity monitoring during business hours rather than overnight — optimizing staffing and alert response times significantly.

**ML-Powered Prioritization** — The Isolation Forest risk score can feed directly into real-time alert systems. Flagging high-value suspicious transactions first maximizes ROI on fraud investigation resources.

**Customer Strategy** — The 46–55 age group and 30–60K income bracket are the core customer base. Marketing teams can tailor loyalty programs and communication strategies specifically for this demographic.

**Revenue Intelligence** — $67.5M in refunds represents nearly 10% of total spend — a significant leakage that warrants deeper investigation into return fraud and policy abuse patterns.

---

## ▶️ How to Run

### Prerequisites
```bash
pip install pandas numpy scikit-learn openpyxl
```

### Steps
```bash
# 1. Clone the repo
git clone https://github.com/yourusername/fraudscope-dashboard.git
cd fraudscope-dashboard

# 2. Add dataset files to the project folder
# transactions_data.csv, users_data.csv, cards_data.csv
# mcc_codes.json, train_fraud_labels.json

# 3. Run the pipeline in order
python step1_eda.py      # Data cleaning & exploration
python step2_sql.py      # SQL aggregations → Excel export
python step3_ml.py       # ML fraud scoring → Excel export

# 4. Open Power BI Desktop
# Get Data → Excel Workbook → financial_dashboard_data.xlsx
# Load all 10 sheets → Open FraudScope.pbix
```

### Output Files Generated
```
financial_dashboard.db            ← SQLite database with all aggregated tables
financial_dashboard_data.xlsx     ← 10-sheet Excel file (Power BI data source)
```

---

## 👤 Author

**[Your Name]**  
📧 your.email@gmail.com  
🔗 [LinkedIn](https://linkedin.com/in/yourprofile) · [GitHub](https://github.com/yourusername)

---

⭐ *If you found this project useful, please consider giving it a star — it helps others find it too!*
