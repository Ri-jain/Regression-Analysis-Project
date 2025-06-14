# 🛍️ Retail Analytics Project | Apparel & Accessories Retailer

📍 **Focus:** Boston Metro Area | 🗓️ Timeline: Spring 2025  
📊 **Tools Used:** Python · Excel · Tableau · Scikit-learn · Matplotlib · SciPy  
📁 **Files Included:** Cleaned customer data, sales records, stores metadata, entity-relationship diagram, regression models, statistical analysis

---

## 🧠 Project Overview

This project analyzes customer and transaction data from a U.S.-based apparel & accessories retailer to understand profitability and customer satisfaction across four store locations in the Boston metro area. It spans data modeling, cleaning, manipulation, hypothesis testing, and regression modeling to guide data-driven business decisions.

---

## 🧱 Part 1: Data Engineering & Exploratory Analysis

### 📌 1. Data Modeling
- Created an **Entity Relationship Diagram (ERD)** linking `customers`, `sales`, and `stores` using primary/foreign keys.
- **Key Fields:**
  - `customer_id` (PK in customers, FK in sales)
  - `store_id` (PK in stores, FK in sales)
  - `product_category`, `price_category`, `gross_margin`

### 🧹 2. Data Inspection & Cleaning
- **Categorical Fixes:** Standardized inconsistent labels in gender, state, and satisfaction scores per data dictionary.
- **Numerical Fixes:** Imputed missing income & age values; removed extreme sale.amount outliers using boxplot/z-score thresholds.

### 🔄 3. Data Wrangling
- Created `customer_purchases.csv` summarizing number of items and average sale price per customer.
- Joined transaction-level and customer-level data to form a unified analysis-ready `customer_purchases` dataframe.

### 📊 4. Visualization & Descriptive Stats
- Calculated mean, median, std dev, and skewness of sale amounts.
- Created **boxplots** to explore distribution by product category and to identify outliers.
- Computed **blended gross margin** per category:
  
  \[
  \text{GM\%} = \frac{\sum(\text{sale.amount} - \text{ext.cost})}{\sum(\text{sale.amount})}
  \]

- Recommended handling methods for outliers (e.g., retention, transformation, or exclusion based on context).

---

## 📈 Part 2: Inference & Predictive Modeling

### ✅ Hypothesis Testing
- **Hypothesis:** “The mean gross margin percentage (GM%) differs significantly across product categories.”
- Conducted **ANOVA test** → Statistically significant differences found across categories.
- **Business Implication:** Promotions should be tailored by category to avoid over-discounting profitable lines.

### 🔍 Regression Analysis
- Built a **multiple linear regression model** to predict `gross_margin` using:
  - `product_category`, `price_category`, `store_id`, `sale.amount`, `month`
- Achieved **R² ≈ 0.18**, offering predictive utility and interpretability.
- **Key Drivers Identified:**
  - Price category had a negative relationship with margin (discounted items)
  - Store ID showed variation in margin performance, revealing operational inefficiencies

---

## 📊 Tableau Dashboard (Coming Soon)
Will include:
- Store-wise gross margin trend
- Category-level profitability
- Customer satisfaction & purchase behavior segmentation

6. Regression analysis. 

Build a regression model to predict gross.margin using any other data that is available to you. Because of the high inherent variability in the outcome measure, achieving an R2 of ~0.15 or higher is considered useful to the firm. However, the model should also provide interpretable results, e.g., information about which variables most affect gross.margin in a positive or negative direction.
