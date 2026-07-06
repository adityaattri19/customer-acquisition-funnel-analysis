 # Customer Acquisition Funnel & Growth Analysis

**Identifying What Drives Customer Value and Churn Across Acquisition Channels**

---

## Table of Contents
1. [Project Background](#project-background)
2. [Data Structure & Initial Checks](#data-structure--initial-checks)
3. [Executive Summary](#executive-summary)
4. [Insights Deep Dive](#insights-deep-dive)
5. [Recommendations](#recommendations)
6. [Assumptions & Caveats](#assumptions--caveats)
7. [Technical Stack](#technical-stack)
8. [Repository Structure](#repository-structure)

---

## Project Background

A company is acquiring customers through five channels: Organic Search, Google Ads, Facebook Ads, Email Campaigns, and Referrals. Despite spending resources on customer acquisition, the business faces inconsistent retention and unclear channel ROI. The growth team lacks a data-driven framework to identify which channels bring the most valuable customers and what factors are driving churn.

This project analyzes 14,900 customer records to build a complete picture of the customer acquisition funnel, from the moment a customer is acquired to whether they stay or leave. The goal is to answer one core business question:

> *Are we acquiring the right customers, through the right channels, and keeping them long enough to be profitable?*

Insights and recommendations are provided across four key areas:

- **Acquisition Channel Performance:** Which channels bring customers at the lowest cost and highest lifetime value?
- **Churn Drivers:** What behavioral and experiential factors most strongly predict whether a customer will leave?
- **Revenue Segmentation:** Which customer segments (premium vs regular, annual vs monthly) generate the most revenue?
- **Engagement Patterns:** How do visit behavior, session time, and email engagement connect to actual spending?

The Python notebook used for all data cleaning and feature engineering is [here](cleaning.py).

The full SQL analysis with query results and screenshots is [here](SQL_Analysis_Report.pdf).

The individual query result exports are in the [query_results](query_results/) folder.

---

## Data Structure & Initial Checks

The analysis uses a single synthetic CRM and digital marketing dataset sourced from Kaggle, simulating a real company's customer database.

| Detail | Info |
|---|---|
| Source | Kaggle, Sales and Marketing Customer Dataset |
| Raw rows | 15,000 |
| Rows after cleaning | 14,900 |
| Original columns | 30 |
| Final columns | 35 |
| Target variable | `churn` (1 = churned, 0 = active) |

The dataset includes customer demographics, behavioral engagement data, transactional records, marketing spend per user, and satisfaction metrics. It intentionally includes real-world data quality issues such as missing values, outliers, inconsistent date formats, and class imbalance in the churn variable.

Before analysis, the following data quality decisions were made:

- **City column removed:** City and country values were mismatched throughout the raw data. City-level analysis would have produced misleading results, so the column was dropped entirely and country was retained for regional analysis.
- **Negative and extreme ages removed:** The raw age column contained negative values and entries above 90, both of which are impossible or implausible for real customers. These were removed before filling remaining nulls with the median.
- **Missing values treated by column context:** Numeric columns with missing values were filled with the median rather than mean, since spending and satisfaction data is typically skewed by outliers. Missing coupon codes were filled with 'NO_COUPON' as their absence simply indicates no coupon was used.
- **Never purchased flag created:** Customers with no last purchase date were not dropped. Instead, a binary flag `never_purchased` was created to distinguish customers who signed up but never bought from those who bought and later churned. These represent different business problems.

---

## Executive Summary

### Overview of Findings

All five acquisition channels have nearly identical CAC (ranging from $17.22 to $17.82), meaning the business spends roughly the same amount to acquire a customer regardless of source. However, what happens after acquisition varies significantly. Referral and Email channels produce the highest LTV customers, while Organic, despite being the cheapest channel, has the highest churn rate at 15.93%. The single strongest churn predictor in the dataset is satisfaction score: customers scoring 1 or 2 churn at rates above 48%, while those scoring 4 or 5 churn at under 10%. Support ticket volume above 4 is the second strongest churn signal, with churn rates jumping sharply from around 13% to above 49% at 5 or more tickets.

For the growth team, 101 actionable insights emerge across 15 SQL queries covering acquisition, retention, revenue, and engagement analysis.

---

## Insights Deep Dive

### Acquisition: CAC Is Nearly Equal Across All Channels, LTV Is Not

- All five channels have an average CAC within a $0.60 range ($17.22 for Organic to $17.82 for Referral), meaning acquisition cost alone cannot differentiate channel quality.
- Referral produces the highest avg LTV at $1,249.37 and Email follows at $1,244.05, while Google Ads sits lowest at $1,216.61.
- The LTV to CAC ratio tells the clearest story: Organic leads at 71.53x, followed by Email at 70.72x and Referral at 70.12x. Google Ads and Facebook Ads trail at 69.38x and 69.99x respectively.
- However, Organic's high ratio is undermined by its churn rate, meaning the LTV figure is not fully realized for a meaningful portion of organic customers.

### Churn: Satisfaction Score Is the Dominant Predictor

- Customers with a satisfaction score of 1 churn at 48.46% and score 2 at 50.17%. Customers scoring 3, 4, and 5 churn at 9.61%, 9.09%, and 9.76% respectively.
- The gap between score 2 and score 3 is not gradual. It is a cliff: churn drops by more than 40 percentage points between dissatisfied and neutral customers. This means satisfaction recovery, not just satisfaction improvement, is the critical intervention point.
- Organic channel has the highest churn at 15.93%, followed by Referral at 15.80%. Facebook Ads has the lowest at 14.79%.
- Support tickets show a clear threshold effect. At 0 to 4 tickets, churn stays between 12% and 14%. At 5 tickets it jumps to 49.91%, at 6 to 50.61%, and at 7 to 55.56%. Customers with 5 or more support tickets are effectively in a high-risk churn zone.

### Revenue: Premium and Subscription Differences Are Smaller Than Expected

- Premium users (4,536 customers) have avg spend of $512.05 and avg LTV of $1,241.17 versus regular users at $508.14 and $1,233.62. The gap exists but is narrower than expected, suggesting premium status alone does not dramatically change spending behavior.
- Monthly subscribers slightly outspend Annual subscribers on avg ($506.33 vs $512.46) but Annual subscribers have lower churn (15.10% vs 15.57%), making Annual customers more stable over time.
- Discount users and non-discount users show nearly identical LTV ($1,239.22 vs $1,232.69), meaning discounts are not meaningfully eroding long-term customer value.
- Organic channel generates the highest total revenue at $1,559,419.43, driven by its slightly higher customer volume rather than higher per-customer spending.

### Engagement: All Channels Are Uniform, Visit Volume Has Limited Spending Impact

- All five channels produce an identical avg engagement score of 0.47, with avg visits ranging from 14.9 to 15.2 and avg session times between 7.9 and 8.1 minutes. Engagement behavior is driven by individual customer patterns, not by where they came from.
- Medium visit customers (20 to 50 visits) have higher avg spend ($515.10) than low visit customers ($508.51), but the high and very high visit segments were not represented in the data, meaning almost all 14,900 customers fall under 50 visits. Visit frequency is not a reliable standalone spending predictor.
- Desktop generates the highest avg revenue per visit at $36.84, followed by Tablet at $36.54 and Mobile at $36.35. The difference is marginal, suggesting device type does not significantly influence purchase behavior.

---

## Recommendations

Based on the SQL analysis conducted across 14,900 customer records, the following actions are recommended for the growth and customer success teams:

- **Prioritize referral program investment and introduce structured referral incentives.** Referral customers have the highest absolute LTV at $1,249.37. Incentivizing existing customers to refer through discounts or cashback creates a compounding acquisition loop at a cost comparable to all other channels.

- **Build a dedicated onboarding sequence for Organic channel customers.** Organic has the best LTV to CAC ratio but the highest churn rate. The business is acquiring these customers cheaply and then losing them. A structured first-week email or product walkthrough sequence targeted specifically at organic signups could close this gap without requiring additional acquisition spend.

- **Set up automated satisfaction score monitoring and intervention.** The churn cliff between score 2 and score 3 (50% vs 9%) is the single highest-leverage intervention point in this entire dataset. An automated alert that triggers a support outreach or recovery offer when a customer drops to a score of 2 or below could have an outsized impact on overall retention rate.

- **Create a premium upgrade campaign targeting high-engagement regular users.** Premium users churn less and spend more. Regular users with high visit counts and session times but who have not upgraded are the most natural conversion targets. A time-limited upgrade offer to this specific segment is likely to convert at higher rates than a broad campaign.

- **Flag and escalate customers with 5 or more support tickets immediately.** The data shows a sharp churn threshold at 5 tickets. Customers at this point should be automatically routed to a senior support tier or customer success manager rather than continuing through standard resolution queues.

- **Introduce conversion nudges for high-visit low-spend customers.** Q14 shows that more visits do not reliably translate to more spending. Exit-intent offers, personalized product recommendations, or limited-time discounts targeted at customers with above-average visits but below-average spend could convert browsing behavior into actual revenue.

---

## Assumptions & Caveats

Throughout the analysis, several assumptions were made to manage data limitations. These are noted below:

- **Synthetic dataset:** This dataset was generated to simulate a real CRM and marketing system. Patterns and correlations reflect the generation logic of the dataset, not a real company's actual customer behavior. Findings are directionally valid for learning purposes but should not be treated as production business intelligence.

- **City column exclusion:** City and country values were mismatched in the raw data. The city column was dropped entirely. Any city-level analysis is not possible with this dataset in its current form.

- **CAC treated as marketing spend per user:** The dataset provides `marketing_spend_per_user` as a direct field. This is treated as CAC throughout the analysis. In a real business setting, CAC would require additional cost inputs such as sales team costs, creative production, and platform fees beyond media spend.

- **Engagement score is a derived metric:** The engagement score was engineered as a weighted combination of total visits (40%), avg session time (30%), and email open rate (30%). The weights reflect a reasonable business judgment but are not derived from a statistical model. Different weight choices would produce different scores.

- **Satisfaction score missing values filled with median:** 702 satisfaction scores were missing and filled with the dataset median. This may slightly compress the distribution of satisfaction scores and understate the true prevalence of very low or very high satisfaction in the customer base.

- **Reference date for recency columns:** Days since last purchase and customer tenure were calculated using April 1, 2025 as a reference date, aligned approximately with the dataset's latest activity period. Using a different reference date would shift these values but not change their relative ordering.

---

## Technical Stack

| Tool | Purpose |
|---|---|
| Python (Pandas, NumPy, Matplotlib) | Data cleaning, feature engineering, and exploratory visualization |
| Google Colab | Cloud-based processing environment, used to avoid hardware limitations |
| MySQL Workbench | SQL analysis across 15 structured queries |
| Excel | Pivot tables and channel comparison charts |
| Power BI / Looker Studio | Interactive dashboard (in progress) |
| Git and GitHub | Version control and portfolio hosting |

---

## Repository Structure

```
customer-acquisition-funnel-analysis/
│
├── cleaning.py                    # Python data cleaning and feature engineering script
├── analysis_queries.sql           # All 15 SQL queries with section comments
├── cleaned_data.csv               # Final cleaned dataset (14,900 rows, 35 columns)
├── SQL_Analysis_Report.pdf        # Full query results with screenshots and findings
├── README.md                      # Project documentation
│
└── query_results/                 # Individual CSV exports for each SQL query
    ├── Q01_customers_by_channel.csv
    ├── Q02_avg_CAC_by_channel.csv
    ├── Q03_avg_LTV_by_channel.csv
    ├── Q04_LTV_CAC_ratio_by_channel.csv
    ├── Q05_overall_churn_rate.csv
    ├── Q06_churn_by_channel.csv
    ├── Q07_satisfaction_vs_churn.csv
    ├── Q08_support_tickets_vs_churn.csv
    ├── Q09_revenue_by_channel.csv
    ├── Q10_premium_vs_regular.csv
    ├── Q11_annual_vs_monthly.csv
    ├── Q12_discount_vs_LTV.csv
    ├── Q13_engagement_by_channel.csv
    ├── Q14_visits_vs_spend.csv
    └── Q15_revenue_per_visit_by_device.csv
```

---

## Contact

**Aditya Attri**
GitHub: [https://github.com/adityaattri19]
LinkedIn: [https://www.linkedin.com/in/aditya-attri19/]
