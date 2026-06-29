# SaaS Subscription Analytics with SQL: Revenue, Churn \& Customer Behavior

## Project Overview

This project analyzes a synthetic multi-table SaaS dataset for **RavenStack**, a fictional AI-powered collaboration platform. The goal is to understand subscription revenue, churn patterns, customer segments, support experience, and product usage behavior.

The analysis is SQL-focused: CSV files are loaded into a temporary SQLite database, and the main business questions are answered using SQL queries. Python is used for data loading, query execution, and visualization.

## Dataset

**Dataset:** SaaS Subscription \& Churn Analytics Dataset  
**Author:** River @ Rivalytics  
**Source:** Kaggle  
**License:** MIT-like  
**Data type:** Fully synthetic, no real personal information

The dataset contains five related CSV tables:

|Table|Rows|Description|
|-|-:|-|
|`ravenstack\_accounts`|500|Customer-level information: industry, country, signup date, referral source, initial plan, seats, trial status, churn flag|
|`ravenstack\_subscriptions`|5,000|Subscription records: plan tier, MRR, ARR, billing frequency, upgrades, downgrades, churn status|
|`ravenstack\_feature\_usage`|25,000|Product usage events linked to subscriptions|
|`ravenstack\_support\_tickets`|2,000|Support interactions linked to accounts|
|`ravenstack\_churn\_events`|600|Churn events with reason codes, refunds, reactivation flags, and feedback|

## Business Questions

This project focuses on the following questions:

1. Which subscription plans generate the most MRR and ARR?
2. What is the current active MRR?
3. What is the overall churn rate?
4. Which customer segments have the highest churn?
5. How do plan tier, billing frequency, referral source, trial status, and seat count relate to churn?
6. Do support tickets, satisfaction scores, response time, or escalations differ between churned and retained customers?
7. Which product features are used most often?
8. Do churned customers show different product usage patterns?
9. What are the most common churn reasons?
10. What business actions could improve retention and recurring revenue?

## Tools Used

* SQL
* SQLite
* Python
* Pandas
* Matplotlib
* Seaborn
* Kaggle Notebook

## Analysis Structure

The notebook is organized into the following sections:

1. Project overview and dataset description
2. Data loading and SQLite setup
3. Data overview and relationship checks
4. Revenue analysis
5. Active MRR analysis
6. Churn analysis
7. Customer segmentation
8. Support experience and churn
9. Feature usage analysis
10. Churn reasons
11. Key findings
12. Business recommendations
13. Conclusion

## Methodology

The dataset was loaded from CSV files into Pandas DataFrames and then written into an in-memory SQLite database. Most analytical calculations were performed in SQL using:

* `JOIN`s across related tables
* `GROUP BY` aggregations
* `CASE WHEN` logic for segmentation
* Common Table Expressions (`WITH` clauses)
* churn rate calculations
* revenue aggregations
* per-account normalization for product usage metrics

Python was used mainly to execute SQL queries and create charts.

## Key Metrics

|Metric|Value|
|-|-:|
|Total accounts|500|
|Total subscription records|5,000|
|Total historical MRR|11,338,747|
|Active MRR|10,159,608|
|Total historical ARR|136,064,964|
|Active ARR|121,915,296|
|Overall account churn rate|22.0%|
|Churned accounts|110|

> Note: Historical revenue is calculated across all subscription records. Active MRR is calculated using active subscription records where `end\_date IS NULL` and `churn\_flag = 0`.

## Key Findings

### 1\. Enterprise is the main revenue driver

Enterprise subscriptions generate the largest share of both total and active MRR.

|Plan tier|Total MRR|Avg MRR|Active MRR|
|-|-:|-:|-:|
|Enterprise|8,473,221|4,917.71|7,546,876|
|Pro|2,105,089|1,256.77|1,924,818|
|Basic|760,437|474.68|687,914|

Although the number of subscriptions is relatively similar across plan tiers, Enterprise has a much higher average MRR. This makes Enterprise the most financially important segment.

### 2\. Revenue is almost evenly split between monthly and annual billing

|Billing frequency|Subscriptions|Total MRR|Avg MRR|
|-|-:|-:|-:|
|Monthly|2,539|5,741,349|2,261.26|
|Annual|2,461|5,597,398|2,274.44|

Both billing models contribute meaningfully to revenue. Annual billing may still be strategically valuable because it can indicate stronger commitment and more predictable cash flow.

### 3\. Churn is meaningful but not strongly explained by plan tier

The overall account churn rate is **22.0%**.

Churn rates are almost identical across initial plan tiers:

|Initial plan tier|Churn rate|
|-|-:|
|Enterprise|22.08%|
|Basic|22.02%|
|Pro|21.91%|

This suggests that plan tier alone is not the main churn driver. Other factors such as acquisition channel, industry, onboarding quality, or product fit may explain churn differences better.

### 4\. Industry and acquisition channel show stronger churn differences

FinTech generates the highest total MRR, while DevTools has the highest churn rate.

|Industry|Total MRR|Churn rate|
|-|-:|-:|
|FinTech|2,669,496|22.32%|
|DevTools|2,403,654|30.97%|
|Cybersecurity|2,253,318|16.00%|
|EdTech|2,043,167|16.46%|
|HealthTech|1,969,112|21.88%|

Referral source also shows meaningful differences:

|Referral source|Total MRR|Churn rate|
|-|-:|-:|
|Organic|2,798,707|17.54%|
|Other|2,377,728|24.27%|
|Ads|2,146,619|23.47%|
|Partner|2,094,243|14.61%|
|Event|1,921,450|30.21%|

Partner-sourced accounts have the lowest churn rate, while event-acquired accounts have the highest churn rate.

### 5\. Trial users and mid-size teams have higher churn

Trial accounts churn more often than non-trial accounts:

|Trial status|Total accounts|Churn rate|
|-|-:|-:|
|Trial|97|25.77%|
|Non-trial|403|21.09%|

Mid-size teams have the highest churn rate:

|Seat segment|Total accounts|Churn rate|
|-|-:|-:|
|Mid-size team|188|25.00%|
|Small team|126|20.63%|
|Large team|186|19.89%|

This may indicate that trial users and mid-size teams need stronger onboarding, clearer value communication, or more targeted retention support.

### 6\. Support experience does not appear to be the primary churn driver

Support metrics are relatively similar between retained and churned accounts.

|Churn flag|Avg tickets per account|Avg resolution time|Avg first response time|Avg satisfaction|Escalation rate|
|-|-:|-:|-:|-:|-:|
|Retained|4.02|35.92 hours|89.50 min|3.97|4.53%|
|Churned|3.93|35.66 hours|84.78 min|4.01|5.56%|

Churned accounts have a slightly higher escalation rate, but satisfaction and response metrics are not worse. Based on this dataset, support experience does not appear to be the main churn driver.

### 7\. Product usage does not clearly explain churn

At first, retained accounts generate more total usage events, but this is mainly because there are more retained accounts in the dataset. After normalizing per account, churned accounts actually show slightly higher average usage.

|Churn flag|Avg usage events per account|Avg usage count per account|Avg errors per account|
|-|-:|-:|-:|
|Retained|49.42|495.13|28.23|
|Churned|52.05|522.04|28.15|

This suggests that churn is not simply caused by low engagement. Some customers may churn despite active usage, which may point to missing features, pricing expectations, competitive alternatives, or product-fit issues.

### 8\. Feature-related issues are the most common churn reason

|Churn reason|Churn events|Share|
|-|-:|-:|
|Features|114|19.00%|
|Support|104|17.33%|
|Budget|104|17.33%|
|Unknown|95|15.83%|
|Competitor|92|15.33%|
|Pricing|91|15.17%|

The most common churn reason is related to features. Pricing is not the leading churn reason, although it still represents a meaningful share of churn events.

### 9\. Most churn events were not preceded by plan changes

|Preceding upgrade|Preceding downgrade|Churn events|
|-|-|-:|
|No|No|432|
|Yes|No|115|
|No|Yes|45|
|Yes|Yes|8|

Most churn events were not preceded by an upgrade or downgrade. However, a noticeable share of churn events happened after an upgrade, which may indicate that some customers did not receive enough additional value after moving to a higher plan.

## Business Recommendations

1. **Prioritize Enterprise retention**  
Enterprise is the strongest revenue driver, so the company should monitor Enterprise accounts closely and create proactive retention workflows for this segment.
2. **Improve onboarding for trial users**  
Trial users have a higher churn rate. The onboarding experience should help them reach product value faster through guided setup, usage-based nudges, and trial success check-ins.
3. **Review event acquisition quality**  
Event-sourced accounts have the highest churn rate. The company should review whether these leads are well-qualified and whether they receive enough post-event onboarding.
4. **Strengthen partner acquisition**  
Partner-sourced accounts have the lowest churn rate. This channel may bring better-fit customers and could be worth expanding.
5. **Investigate DevTools churn**  
DevTools has the highest churn rate by industry. The company should analyze whether this segment has specific feature expectations, integration needs, or competitive alternatives.
6. **Monitor customers after upgrades**  
Some churn events happen after upgrades. Customers who recently upgraded should receive additional support to ensure they understand and use the higher-tier value.
7. **Focus product work on feature gaps**  
Since features are the most common churn reason, product teams should analyze feedback from churned accounts and identify missing or underperforming functionality.
8. **Do not rely only on usage volume as a churn signal**  
Churned accounts do not show lower usage per account. Retention models should combine usage metrics with churn reasons, support signals, acquisition source, plan changes, and customer segment data.

## Limitations

* The dataset is fully synthetic, so findings should be interpreted as analytical practice rather than real business conclusions.
* The data contains multiple subscription records per account, so account-level and subscription-level metrics should be interpreted carefully.
* Active MRR is calculated at the subscription-record level using active subscription records.
* The account-level churn flag indicates whether an account churned at any point, so reactivations and multiple subscriptions may create differences between account-level churn and active subscription metrics.

## Conclusion

This analysis shows that RavenStack’s revenue is mainly driven by Enterprise customers, while churn is not strongly explained by plan tier alone. More meaningful churn differences appear across industries, referral sources, trial status, and seat segments.

The results suggest that retention strategy should focus on high-value Enterprise accounts, trial onboarding, event-acquired customers, DevTools accounts, and product feature gaps. Support experience should continue to be monitored, but in this dataset it does not appear to be the primary churn driver.

Overall, the project demonstrates how SQL can be used to analyze SaaS subscription data across revenue, churn, customer segmentation, support experience, product engagement, and churn reasons.

## How to Run

1. Open the Kaggle Notebook.
2. Make sure the dataset is attached to the notebook.
3. Run all cells from top to bottom.
4. The notebook loads CSV files into Pandas, writes them into an in-memory SQLite database, and executes SQL queries through a helper function.

## Repository Structure

```text
.
├── saas-subscription-analytics.ipynb   # Main analysis notebook
└── README.md                           # Project documentation
```

## Dataset Credit

This project uses the **SaaS Subscription \& Churn Analytics Dataset** created by **River @ Rivalytics**. The dataset is synthetic and distributed for educational and portfolio purposes.

