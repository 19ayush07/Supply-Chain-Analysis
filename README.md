# Supply Chain Delivery Performance & Profitability Analysis

End-to-end analysis of a global e-commerce company's order fulfillment operations — quantifying how late deliveries erode profitability, isolating the operational bottlenecks driving delays, and building a predictive model to flag at-risk orders before they go late.

More than half of all orders (**54.71%**) in this dataset arrived later than their scheduled delivery window. This project traces that problem end-to-end: from raw transactional data, through cleaning and feature engineering, into KPI reporting, bottleneck detection, root-cause analysis, time-based pattern discovery, and finally a Random Forest classifier (**74% accuracy**) that predicts late-delivery risk at the moment an order is placed. The result is a decision-ready business report with prioritized, actionable recommendations.

## Table of Contents

- [Overview](#overview)
- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Tools & Tech](#tools--tech)
- [Architecture / Analysis Pipeline](#architecture--analysis-pipeline)
- [Skills Demonstrated](#skills-demonstrated)
- [Walkthrough](#walkthrough)
- [How to Run](#how-to-run)
- [Results](#results)
- [Full Report](#full-report)
- [Future Work](#future-work)
- [Author & Contact](#author--contact)

## Overview

This is an end-to-end supply chain delivery performance analysis, built on a real-world (Kaggle) supply chain dataset covering multiple product categories and international regions. It goes beyond a typical "clean the data and make some charts" project — it follows the same workflow a data analyst would use inside a company: understand the business problem, clean and engineer the data, quantify the financial impact with KPIs, hunt down the operational root causes, and package everything into a stakeholder-ready report with recommendations. A predictive machine learning layer (Random Forest + SMOTE) is added on top to make the project more advanced and demonstrate that the same analysis can power a real-time "will this order be late?" alert system.

## Business Problem

A global e-commerce platform sells products across categories — sporting goods, fitness equipment, outdoor gear, footwear, and apparel — across multiple international regions.

**The problem:** actual shipping time frequently deviates from the scheduled delivery window. This is:

- Eroding customer trust
- Reducing order profitability
- Preventing the business from making reliable delivery-time commitments to buyers at the point of purchase

**Analytical objectives:**

1. Understand the current state of delivery performance across all dimensions.
2. Quantify the financial impact of delays on order profitability.
3. Identify the primary operational bottlenecks driving late deliveries.
4. Build a predictive model to flag high-risk orders in advance.
5. Deliver actionable recommendations to improve on-time delivery and profitability.

## Dataset

- **Source:** DataCo Smart Supply Chain dataset (Kaggle)
- **Raw size:** ~180,000+ order-line records across 53 columns, combining customer, product, category, and order/shipment data into one master (aggregated) table — representing what would normally live across multiple relational tables in a company's database.
- **After cleaning:** 172,765 orders retained (cancelled orders removed) with ~20 relevant features used for analysis.
- **Key raw fields:** shipping type, scheduled vs. actual shipping days, order profit per order, sales per customer, delivery status, late delivery risk flag, category/department, customer segment, order region, shipping mode, order/shipping dates.
- **Time span:** ~2015 to 2018.

## Tools & Tech

- **Language:** Python (Jupyter Notebook)
- **Data handling:** pandas, NumPy
- **Visualization:** Matplotlib, Seaborn (custom "viridis + danger red" professional color theme for consistent, presentation-ready charts)
- **Machine Learning:** scikit-learn (Random Forest Classifier, train/test split, evaluation metrics), imbalanced-learn (SMOTE for class balancing)
- **Reporting:** Word (.docx) business report generated programmatically (Node.js + Python)

## Architecture / Analysis Pipeline

```
CSV (DataCoSupplyChainDataset.csv)
        │
        ▼
Data Cleaning (drop irrelevant/redundant/no-variance columns, remove cancelled orders, fix date types)
        │
        ▼
Feature Engineering (Order Processing Time, Delay, Is_Delayed, order_month/day/hour, Profitability Flag)
        │
        ▼
Business KPI Calculation (Python)
        │
        ▼
EDA & Visual Analysis (Profitability, Bottleneck Detection, Root Cause Analysis, Time-Based Patterns)
        │
        ▼
Predictive Modeling (Frequency Encoding → SMOTE → Random Forest Classifier)
        │
        ▼
Business Report (.docx) — Executive Summary, KPIs, Findings, Recommendations
```

## Skills Demonstrated

- Business problem framing and translating it into analytical objectives
- Data cleaning at scale (removing redundant, empty, and zero-variance columns; deduplication checks; missing value analysis)
- Feature engineering from date/time fields (processing time, delay, calendar features)
- Categorical encoding for ML (frequency encoding — chosen specifically to handle high-cardinality columns without dimensionality blow-up)
- Handling imbalanced classification targets with SMOTE (applied only to training data, never to the test set)
- Business KPI design and calculation (on-time %, late %, profit at risk, 90th-percentile delay, etc.)
- Root cause / bottleneck analysis using group-by aggregation across multiple operational dimensions
- Time-series/calendar pattern analysis (month, day-of-week, hour-of-day)
- Supervised ML classification (Random Forest) with proper train/test evaluation (accuracy, precision, recall, classification report)
- Professional data storytelling — custom chart theming, annotated visualizations, and translating findings into plain-English business language
- Structured business reporting: Executive Summary → Findings → Root Cause → Recommendations → Conclusion

## Walkthrough

### 1. Cleaning

- Dropped columns that were fully empty (`Product Description`), irrelevant to delivery analysis (images, emails, passwords, names, lat/long, zip codes), pure ID columns, or redundant/zero-variance (`Benefit per order` was identical to `Order Profit Per Order`; `Product Status` had a single value).
- Removed cancelled orders (`Delivery Status == 'Shipping canceled'`) since they carry no delivery-time signal.
- Converted order and shipping date columns to proper datetime.
- **Result:** 172,765 clean rows, ~20 usable features, zero remaining missing values.

### 2. EDA & Feature Engineering

- Checked value counts for all low-cardinality categorical columns (payment type, shipping mode, delivery status, customer segment, order status).
- Engineered `Order Processing Time` (shipping date − order date), `Delay` (processing time − scheduled shipment days), and a boolean `Is_Delayed` flag.
- Extracted `order_month`, `order_day` (name), and `order_hour` from the order date for time-based analysis.
- Created a three-way `Profitability Flag` (Profit / Loss / Break Even) from `Order Profit Per Order`.

### 3. Insights (KPIs, Bottlenecks, Root Causes, Time Patterns)

- Calculated core business KPIs: total orders, late deliveries, late/on-time %, 90th-percentile delay, total profit, and profit lost to delayed orders.
- Measured profitability distribution (Profit vs. Loss vs. Break Even) and profit trend across delay-day buckets.
- Ran bottleneck detection across six operational dimensions: order region, customer segment, shipping mode, order status, payment type, department.
- Built a reusable `top_drivers_for_region()` function to isolate the top 10 delay-driving factors for any specific region (demonstrated on Central Africa and East Africa).
- Ran time-based analysis across month, day-of-week, and hour-of-day to surface seasonal and behavioral delay patterns.

### 4. Reporting (in place of a Dashboard)

Rather than jumping straight to a BI dashboard, the analysis was first validated in Python and packaged into a structured Word (.docx) business report — Executive Summary, Business Context, KPIs, Profitability Analysis, Bottleneck Detection, Root Cause Analysis, Time-Based Patterns, ML Results, Strategic Recommendations, and Conclusion. This mirrors real company workflow, where a dashboard is only built after the underlying analysis and reporting are reviewed and approved by stakeholders.

## How to Run

1. Clone this repository and place `DataCoSupplyChainDataset.csv` in the project root.
2. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn imbalanced-learn
   ```
3. Open and run `Supply_chain_Complete_Analysis.ipynb` top to bottom in Jupyter Notebook / JupyterLab.
4. Charts render inline; KPIs and model evaluation print directly in notebook output cells.
5. The accompanying `Supply_Chain_Project_Report.docx` documents the findings in business-report format for non-technical stakeholders.

## Results

| KPI | Value |
|---|---|
| Total Orders | 172,765 |
| Late Deliveries | 94,523 |
| Late Delivery % | 54.71% |
| On-Time Delivery % | 45.29% |
| Total Profit | ~$7.5M |
| Profit at Risk (delayed orders) | ~$2.1M |
| Profitable Orders | 80.7% |
| Loss-Making Orders | 18.7% |
| Break-Even Orders | 0.6% |
| Random Forest Accuracy | 74% |
| Random Forest Precision | 78% |
| Random Forest Recall | 75% |

**Top findings:** First Class shipping mode has a ~100% delay rate; Central Africa has the highest regional delay rate (~58.7%), though all regions cluster tightly between 55–59% (ruling out a localized issue and confirming a company-wide systemic problem); Health & Beauty is the most delay-prone department; December, August, and September see the highest seasonal delay rates; Monday and Sunday orders, and late-evening/afternoon orders, are delayed most often.

## Full Report

### 1. Executive Summary

The analysis covers 172,765 orders placed between roughly 2015 and 2018. Of these, 94,523 orders (54.71%) were delivered later than their scheduled window — this is not an edge case, it is the default experience for the majority of customers. Delayed orders collectively sit on ~$2.1M of profit under continued operational risk, out of a total profit pool of roughly $7.5M. A Random Forest predictive model, trained only on information available at the moment an order is placed, achieved 74% accuracy in identifying at-risk orders before they become late — meaning a working early-warning system is achievable with the data already on hand, not a future aspiration.

### 2. Business Context & Objectives

The company operates a global e-commerce platform selling sporting goods, fitness equipment, outdoor gear, footwear, and apparel across multiple international regions. Actual shipping time frequently deviates from the scheduled delivery window, which is eroding customer trust, reducing order profitability, and preventing the business from making reliable delivery commitments at the point of purchase. The analysis was scoped to: understand current delivery performance across all dimensions, quantify the financial impact of delays, identify the operational bottlenecks driving late deliveries, build a predictive risk model, and deliver actionable recommendations.

### 3. Key Performance Indicators (KPIs)

| Metric | Value |
|---|---|
| Total Orders | 172,765 |
| Late Deliveries | 94,523 |
| Late Delivery % | 54.71% |
| On-Time Delivery % | 45.29% |
| Total Profit | ~$7.5M |
| Total Profit Tied Up in Delayed Orders | ~$2.1M |
| 90th Percentile Delay | ~3 days (even the most extreme delay cases are contained within 3 days) |
| Average Order Profit | ~$22 |

These KPIs were computed directly in Python (the same numbers a Power BI or Tableau dashboard card would show), following the practice of validating analysis in code before any dashboard is built.

### 4. Profitability Analysis

Order-level profitability was classified into three tiers based on `Order Profit Per Order`:

- 80.7% of orders are profitable
- 18.7% are loss-making
- 0.6% are break-even (net-zero)

A profit-vs-delay-days analysis shows mean profit per order remains fairly stable (roughly $20–$23) regardless of how many days an order is delayed — meaning delay length by itself is not the primary driver of per-order loss; profitability is driven more by pricing/cost structure than by shipping delay severity. One mild anomaly: orders delayed by around 4 days showed a slightly higher profit than shorter delays, which doesn't fit a simple "later = worse" narrative and is worth further investigation rather than assuming causation.

### 5. Bottleneck Detection

Delay percentage was computed across six operational dimensions — order region, customer segment, shipping mode, order status, payment type, and department — to see where the problem concentrates.

- **Shipping Mode is the #1 lever:** First Class shipping shows a ~100% delay rate; Standard Class sits around ~39%.
- **Regional spread is narrow:** Central Africa leads at ~58.7%, but all regions cluster between 55–59%. This narrow spread rules out a localized logistics failure at any single region and confirms the issue is company-wide and systemic.
- **No segment is spared:** Consumer, Corporate, and Home Office customer segments all experience essentially the same broken delivery promise (Home Office slightly higher, ~55.4%).
- Certain departments lag marginally more (e.g., Health & Beauty, Pet Shop) — worth an inventory/carrier audit for these specific product categories.

### 6. Root Cause Analysis

A reusable driver-analysis function was built to surface the top 10 factors most associated with delay within any given region.

- **Central Africa:** top drivers include First Class and Second Class shipping modes, orders stuck in "Payment Review," order status "Pending Payment," payment type "Transfer," and department "Outdoors" and "Golf," concentrated in the Consumer segment.
- **East Africa:** top drivers include department "Fitness," order status "Pending"/"Closed," and payment type "Cash" (i.e., cash-on-delivery orders are also arriving late).

The recurring theme: payment-status friction (pending/under-review payments) and shipping-mode misconfiguration show up as top drivers across multiple regions — these are addressable, process-level issues rather than one-off failures.

### 7. Time-Based Delay Patterns

With ~3 years of data (2015–2018), delay percentage was analyzed by month, day of week, and hour of day:

- **Monthly:** July shows the lowest delay rate; December, August, and September show the highest — December's spike is consistent with holiday/festive-season volume pressure.
- **Day of week:** Monday and Sunday orders see the highest delay rates.
- **Hour of day:** Delay is lowest around 7 AM order placement; it is highest for orders placed in the late evening (~57.1% delay rate) and around midday/early afternoon — suggesting off-peak-staffing or batch-processing effects rather than a demand-volume effect alone.

### 8. Machine Learning Model Results

A Random Forest Classifier was trained to predict `Late_delivery_risk` using only features available at the moment an order is placed: shipping type, scheduled shipment days, category, customer segment, department, order region, shipping mode, order month, and order hour.

- High-cardinality categorical features were frequency-encoded (rather than one-hot encoded) to keep the feature space low-dimensional and avoid exploding model complexity/training time.
- The target variable was imbalanced (~57% vs ~43% split), so SMOTE oversampling was applied to the training set only — the test set was left untouched to preserve a realistic evaluation.
- **Results:** Accuracy 74%, Precision 78%, Recall 75% — meaning the model correctly flags the large majority of at-risk orders without generating excessive false positives or false negatives, using only data that's available at order time (i.e., it's deployable, not just descriptive).

### 9. Strategic Recommendations

1. Immediately audit First Class and Second Class shipping capacity — these carry the highest delay rates and the biggest quick-win potential.
2. Deploy the predictive alert system — the model already performs well enough to flag at-risk orders in production.
3. Resolve payment-processing bottlenecks — introduce automated escalation for stalled payment reviews exceeding a defined time threshold (e.g., 2 hours).
4. Build seasonal surge capacity planning for August, September, and December.
5. Default to Standard Class for eligible orders where First/Second Class isn't strictly required.
6. Investigate high-delay departments in African regions (e.g., Outdoors, Golf) with a dedicated inventory/carrier audit.
7. Work to reduce the loss-making order share (currently 18.7%).
8. Retrain the predictive model quarterly, and explore adding carrier-level, weather, and warehouse-utilization features to push recall above 80% within six months.

### 10. Conclusion

The analysis surfaces a clear and urgent picture: a global e-commerce operation where the majority of orders (54.71%) fail to meet their promised delivery windows, putting ~$2.1M of profit at ongoing risk. The root causes are identifiable and addressable — shipping-mode misconfiguration is the dominant operational failure, payment-processing friction creates a secondary bottleneck, seasonal volume spikes aren't adequately planned for, and geographic disparities, while present, are secondary to the systemic, company-wide pattern. A Random Forest predictive model trained on readily-available order features already achieves 74% accuracy, meaning this is a deployable capability today — not a future aspiration.

## Future Work

- Add carrier-level, weather, and warehouse-utilization data to improve model recall beyond 80%
- Experiment with additional classifiers (XGBoost, LightGBM) and hyperparameter tuning
- Build a live Power BI / Tableau dashboard on top of this validated analysis for ongoing monitoring
- Extend root-cause driver analysis to every region, not just the top two
- Investigate the "4-day delay = higher profit" anomaly further to confirm or rule out a causal relationship


