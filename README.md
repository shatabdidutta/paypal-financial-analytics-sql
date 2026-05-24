# paypal-financial-analytics-sql
# PayPal Financial Analytics using Advanced SQL

## Project Overview

The rapid expansion of digital payment systems has transformed the financial industry, creating ecosystems where customers can perform transactions, manage accounts, use payment cards, and access lending services through a single platform. Financial technology companies process millions of transactions every day while simultaneously handling customer engagement, credit assessment, risk management, and operational efficiency.

This project simulates a real-world PayPal-style financial analytics environment using Advanced SQL techniques to analyze customer behavior, account performance, transaction trends, loan portfolios, and card usage patterns. The objective is to convert raw financial data into meaningful business insights that support strategic decision-making.

The project applies industry-level SQL concepts frequently used in fintech, business intelligence, and data engineering environments.

---

## Business Objectives

The project aims to answer critical business questions including:

- Identify high-value customers
- Analyze customer transaction behavior
- Monitor revenue growth trends
- Detect unusual customer activity
- Evaluate customer account performance
- Segment customers by financial risk
- Measure customer lifetime value
- Discover cross-selling opportunities
- Analyze lending portfolios
- Generate actionable business insights

---

## Dataset Description

The project consists of five datasets:

### 1. Customers Table

Contains customer demographic and financial information.

| Column |
|----------|
| customer_id |
| name |
| age |
| gender |
| city |
| state |
| credit_score |
| annual_income |

---

### 2. Accounts Table

Contains customer account details.

| Column |
|----------|
| account_id |
| customer_id |
| account_type |
| balance |
| average_monthly_balance |
| status |

---

### 3. Transactions Table

Stores customer payment and transaction records.

| Column |
|----------|
| txn_id |
| account_id |
| txn_type |
| transaction_mode |
| amount |
| status |
| txn_timestamp |

---

### 4. Cards Table

Contains card-related information.

| Column |
|----------|
| card_id |
| customer_id |
| card_type |
| credit_limit |
| status |

---

### 5. Loans Table

Contains customer lending data.

| Column |
|----------|
| loan_id |
| customer_id |
| loan_type |
| amount |
| interest_rate |
| installment_amount |
| status |

---

## Entity Relationship Diagram (ERD)

```text

Customers
    |
    | customer_id
    ↓
Accounts
    |
    | account_id
    ↓
Transactions

Customers
    |
    | customer_id
    ↓
Cards

Customers
    |
    | customer_id
    ↓
Loans
Skills Demonstrated

This project demonstrates:

Advanced SQL
Data Cleaning
Data Analysis
Window Functions
Ranking Functions
Aggregate Analysis
Common Table Expressions (CTEs)
Subqueries
Conditional Logic
Date Functions
Customer Segmentation
Cohort Analysis
Statistical Analysis
Business Analytics
SQL Business Problems and Solutions
Question 1
Find Top 10 Customers by Total Transaction Amount
WITH customer_transactions AS
(
SELECT
c.customer_id,
c.name,
SUM(t.amount) total_amount

FROM customers c
JOIN accounts a
ON c.customer_id=a.customer_id
JOIN transactions t
ON a.account_id=t.account_id

GROUP BY
c.customer_id,
c.name
)

SELECT *
FROM customer_transactions
ORDER BY total_amount DESC
LIMIT 10;
Insight

High-value customers contribute significantly to platform revenue and can be targeted for premium financial products.

Question 2
Monthly Revenue Trend with Running Revenue
WITH monthly_revenue AS
(
SELECT
DATE_TRUNC(DATE(txn_timestamp),MONTH) month,
SUM(amount) revenue

FROM transactions
WHERE status='completed'

GROUP BY month
)

SELECT
month,
revenue,

SUM(revenue)
OVER(
ORDER BY month
)
AS cumulative_revenue

FROM monthly_revenue;
Insight

Shows growth trends and revenue accumulation over time.

Question 3
Find Customers with Balance Below Average
SELECT
customer_id,
account_id,
balance

FROM accounts

WHERE balance <
(
SELECT AVG(balance)
FROM accounts
);
Insight

Customers with low balances may require retention or engagement campaigns.

Question 4
Rank Customers by Balance within Account Type
SELECT
customer_id,
account_type,
balance,

DENSE_RANK()
OVER
(
PARTITION BY account_type
ORDER BY balance DESC
)
AS balance_rank

FROM accounts;
Insight

Identifies top customers within each account category.

Question 5
Find Most Popular Transaction Modes
SELECT
transaction_mode,

COUNT(*) total_transactions,

ROUND(
100*COUNT(*)/
SUM(COUNT(*))
OVER(),2
)
AS percentage_share

FROM transactions

GROUP BY transaction_mode
ORDER BY total_transactions DESC;
Insight

Helps understand customer payment preferences.

Question 6
Detect High Transaction Activity Customers
WITH customer_txns AS
(
SELECT
c.customer_id,
COUNT(*) transaction_count

FROM customers c
JOIN accounts a
ON c.customer_id=a.customer_id
JOIN transactions t
ON a.account_id=t.account_id

GROUP BY c.customer_id
),

stats AS
(
SELECT
AVG(transaction_count) avg_txn,
STDDEV(transaction_count) std_txn

FROM customer_txns
)

SELECT
ct.customer_id,
ct.transaction_count

FROM customer_txns ct
CROSS JOIN stats s

WHERE transaction_count >
(avg_txn+(2*std_txn));
Insight

May indicate fraud patterns or heavy platform usage.

Question 7
Loan Risk Segmentation
SELECT
customer_id,
amount,
interest_rate,

CASE

WHEN interest_rate >12
THEN 'High Risk'

WHEN interest_rate BETWEEN 8 AND 12
THEN 'Medium Risk'

ELSE 'Low Risk'

END AS risk_level

FROM loans;
Insight

Helps categorize borrowers for risk assessment.

Question 8
Active Customers with Loans and Cards
SELECT DISTINCT
c.customer_id,
c.name

FROM customers c

JOIN cards cd
ON c.customer_id=cd.customer_id

JOIN loans l
ON c.customer_id=l.customer_id

WHERE cd.status='active'
AND l.status='active';
Insight

Strong candidates for cross-selling financial products.

Question 9
Customer Lifetime Value
SELECT
c.customer_id,
c.name,

SUM(t.amount)
AS customer_lifetime_value

FROM customers c

LEFT JOIN accounts a
ON c.customer_id=a.customer_id

LEFT JOIN transactions t
ON a.account_id=t.account_id

GROUP BY
c.customer_id,
c.name

ORDER BY customer_lifetime_value DESC;
Insight

Identifies customers contributing maximum long-term value.

Question 10
Customer Cohort Analysis
SELECT
EXTRACT(YEAR FROM join_date)
AS cohort_year,

COUNT(customer_id)
AS total_customers,

AVG(credit_score)
AS avg_credit_score,

AVG(annual_income)
AS avg_income

FROM customers

GROUP BY cohort_year
ORDER BY cohort_year;
Insight

Measures customer acquisition quality over time.

Key Findings
Customer Insights
A small customer segment contributes a large portion of revenue
Higher income customers tend to maintain larger balances
Active customers frequently use multiple financial products
Transaction Insights
Digital payment methods dominate transaction behavior
Revenue shows cumulative growth patterns
Some customers exhibit unusually high activity
Lending Insights
Medium-risk borrowers form the largest portfolio segment
Cross-selling opportunities exist among active customers
Risk Insights
Transaction anomalies may indicate suspicious behavior
Risk segmentation supports credit decision-making
Tech Stack
SQL
BigQuery
GitHub
Relational Databases
Repository Structure
paypal-financial-analytics/
│
├── datasets/
│      customers.csv
│      accounts.csv
│      transactions.csv
│      cards.csv
│      loans.csv
│
├── sql_queries/
│      analysis.sql
│
├── README.md
│
└── assets/
       erd.png
Conclusion

This project demonstrates how Advanced SQL can be applied to solve real-world fintech business problems. The analysis combines customer intelligence, financial analytics, lending assessment, and risk analysis techniques to generate actionable insights similar to those used by modern digital payment companies.
