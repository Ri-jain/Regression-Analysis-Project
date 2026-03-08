# Retail Analytics — Boston-Based Apparel Store

**Academic Project | MS Business Analytics, Northeastern University**

> A full-cycle retail analytics project spanning data cleaning, gross margin analysis, hypothesis testing, and multi-model regression — applied to 42,000+ sales transactions across 4 store locations in the Boston metro area.

---

## Project Overview

This project analyses customer and transaction data from a U.S.-based apparel and accessories retailer to understand what drives gross margin variability across stores, product categories, pricing strategies, and seasons. The goal was to move from raw, messy data to actionable business recommendations supported by statistical evidence.

**Business Questions Answered:**
- Does Q1 (January–March) have a statistically significantly lower gross margin than the rest of the year?
- What combination of factors — pricing, product category, store location, and seasonality — best explains gross margin performance?
- Where should the business focus to protect and grow profitability?

---

## Tools & Technologies

| Layer | Tools |
|---|---|
| Data Cleaning & Wrangling | Python (Pandas), R (dplyr, stringr) |
| Statistical Analysis | Python (Statsmodels, SciPy), R (base stats) |
| Machine Learning / Regression | Python (Scikit-learn), R (caret, lm) |
| Visualisation | Python (Matplotlib, Seaborn), R (ggplot2) |
| Data Files | Excel (.xlsx), CSV |

---

## Dataset

| File | Description |
|---|---|
| `sales (1).xlsx` | 42,000+ transaction-level records (store, SKU, category, qty, revenue, cost, margin) |
| `customers.xlsx` | Customer profiles (state, age, birthday month, loyalty status, in-store experience) |
| `stores.csv` | Store reference data (location, size, tier) |
| `customer_purchases.csv` | Engineered: summarised customer-level purchase behaviour |
| `updated_sales.csv` | Cleaned sales data post-outlier handling (Phase 1) |
| `updated_sales2.csv` | Sales data with dummy variables added for regression |

**Data relationships:** Customers → Sales (1:Many via `customer.id`), Stores → Sales (1:Many via `store`)

---

## Part 1 — Data Engineering & Exploratory Analysis

### Data Cleaning

**Customers file:**
- Standardised 6+ variants of state names (e.g. `"Mass."`, `"Massachusets"`, `"Massachusetts "`) to valid 2-letter abbreviations
- Removed records where `age = 0` (logically invalid)
- Converted `birthday.month` from string variants (`"October"`, `"Mar"`, `"Apr."`) to integers
- Split `in.store.exp` and `selection` columns into numeric scores and text labels (R) / filled nulls with `'Blank (missing data)'` (Python)

**Sales file:**
- Coerced `tran.number`, `sku`, `category` to string type
- Converted `loyalty.member` to binary integer (0/1)
- Filled null `customer.id` with `'NA'` — preserving non-loyalty transactions rather than dropping them

**Key principle:** Clean each file independently before joining. Dirty data from one file corrupts the other if you join first.

### Outlier Handling

Outliers were identified using two independent methods:
- **IQR / Boxplot method** — flags values beyond Q1 − 1.5×IQR or Q3 + 1.5×IQR
- **Z-score method** — flags values with |Z| > 3

**Category-specific thresholds applied (R):**

| Category | Threshold | Reasoning |
|---|---|---|
| Men's Apparel | Removed > $160 | Narrower typical price range; above $160 was anomalous |
| Gifts & Lifestyle | Removed > $80 | Smallest transactions; $80+ was genuinely extreme |
| Footwear | Removed > $250 | Higher-ticket category; $250 is a defensible ceiling |
| All others | Retained | No values extreme enough to warrant removal |

A single extreme outlier ($693) in Python was replaced with the category mean. All other flagged values were retained as legitimate high-value transactions.

### Gross Margin by Category

| Category | Total Revenue | Blended GM% |
|---|---|---|
| Men's Apparel | $103,847 | **65.5%** ← Highest |
| Footwear | $204,103 | 63.4% |
| Women's Apparel | $226,275 | 63.3% |
| Childrens | $27,191 | 63.1% |
| Accessories | $45,034 | 62.4% |
| Gifts & Lifestyle | $9,968 | **57.7%** ← Lowest |

---

## Part 2 — Statistical Inference & Regression Modelling

### Hypothesis Testing — Is Q1 Significantly Worse?

**Test:** Left-tailed OLS regression test using a binary `is_q1` predictor

| Component | Statement |
|---|---|
| H₀ | Mean GM% for Q1 ≥ Mean GM% for Q2–Q4 |
| Hₐ | Mean GM% for Q1 < Mean GM% for Q2–Q4 |
| Result | **p < 0.05 → Reject H₀** |
| Conclusion | Q1 gross margin is statistically significantly lower than the rest of the year |

The monthly regression model confirmed **February** as the lowest-margin individual month and **December** as the highest (+0.270 coefficient vs. January baseline).

> Note: R² = 0.16 for the hypothesis testing model — this is expected. The purpose was to isolate the Q1 effect via p-value, not to build a predictive model.

---

### Regression Modelling — Six-Model Iteration Process

Rather than building one model and stopping, the team iterated through six models, each addressing a specific problem with the previous one.

| Model | Formula Focus | Key Finding / Issue |
|---|---|---|
| Model 1 — Baseline | All predictors including `price.category` | High significance, but `price.category` is data leakage — it encodes the pricing decision that directly produces gross margin |
| Model 2 — Interactions + Quadratic | `price.category × category`, `qty²`, `unit.cost²`, `sale.amount²` | `qty²` dropped due to singularity — after outlier removal, near-zero variance in qty made the quadratic term redundant |
| Model 3 — Monthly Dummies | Month as dummy variables (Jan = baseline) | Seasonality confirmed significant — monthly dummies correctly avoid the false linearity assumption of month as a continuous number |
| Model 4 — Loyalty Test | Added `loyalty.member` | Insignificant — loyalty membership does not independently affect gross margin |
| Model 5 — Loyalty × Qty Interaction | `loyalty × qty` interaction term | Also insignificant — loyalty status does not change the margin effect of quantity purchased |
| **Model 6 — Final (log_model)** | `interaction_log = log(sale.amount) × log(ext.cost)` | **Best fit. Store effects become significant. R² = 0.72** |

### Final Model

```
gross.margin ~ store + qty + unit.cost + unit.original.retail +
               interaction_log + month_2 + month_3 + ... + month_12
```

**Why the log interaction term?**

`sale.amount` and `ext.cost` are correlated — higher-cost items tend to sell for more. Including both separately creates multicollinearity. The log transformation compresses their scales and the interaction captures their joint effect on margin. Critically: when a non-log version was tested, store coefficients became insignificant. The log version restored store-level signal — confirming the transformation was doing real analytical work.

### Model Performance

| Metric | Value |
|---|---|
| R² (Training) | **0.72** |
| Adjusted R² | 0.72 |
| F-Statistic | 570.3 (p < 0.001) |
| Residual Standard Error | 0.257 |

**Train/Test split:** 80/20 stratified on `gross.margin` using `createDataPartition` (caret). Model validated on held-out test set using MAE, RMSE, MSE, MAPE.

### Key Regression Findings

| Driver | Effect | Business Meaning |
|---|---|---|
| Full Price vs. Clearance | +0.086 | Clearance is margin destruction — full price is the floor |
| Markdown vs. Clearance | +0.097 | Even markdowns beat clearance |
| Men's Apparel | +0.202 | Highest-margin category — underinvested relative to profitability |
| Women's Apparel | +0.114 | Strong margin + highest revenue — core of the business |
| Childrens | −0.306 | Largest negative category effect |
| Gifts & Lifestyle | ~0 (p = 0.97) | No margin advantage over baseline |
| Store 14 | −0.070 | Worst-performing location — warrants operational review |
| Quantity | −2.013 (+ qty² +0.642) | Bulk discounting hurts margin, but effect diminishes at high volumes — non-linear |
| December | +0.270 | Strongest seasonal month — maximise inventory depth |
| February | −0.036 | Worst individual month — post-holiday structural drag |

---

## Business Recommendations

1. **Eliminate clearance as a default** — implement markdown trigger rules (e.g. 60-day age threshold) to avoid inventory reaching clearance pricing
2. **Grow Men's Apparel** — highest GM% (65.5%) but lowest revenue among major categories; increase inventory investment and floor space
3. **Review Gifts & Lifestyle** — statistically indistinguishable from the baseline; fix margin structure or reduce category footprint
4. **Build a Q1 margin defence plan** — begin post-holiday clearance planning in November; use loyalty promotions (bundles, point multipliers) rather than price cuts in January–February
5. **Investigate Store 14** — largest negative store coefficient; audit price realisation vs. other locations
6. **Protect December inventory** — holiday season is the highest-margin window; stockouts cost full-price revenue

---

## Repository Structure

```
Regression-Analysis-Project/
│
├── sales (1).xlsx          # Raw transaction data
├── customers.xlsx          # Raw customer profiles
├── stores.csv              # Store reference data
├── customer_purchases.csv  # Engineered customer-level summary
├── updated_sales.csv       # Cleaned sales (post-outlier handling)
├── updated_sales2.csv      # Sales with dummy variables for regression
└── README.md               # This file
```

---

## Author

**Rishabh Jain**
MS Business Analytics (Statistics) — Northeastern University D'Amore-McKim School of Business
[LinkedIn](https://linkedin.com/in/rishabhjainisme) | [GitHub](https://github.com/Ri-jain)
