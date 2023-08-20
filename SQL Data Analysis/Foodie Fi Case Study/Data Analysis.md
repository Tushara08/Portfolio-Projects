# Data Analysis

1. How many customers has Foodie-Fi ever had?

***
```sql

SELECT COUNT(DISTINCT customer_id) AS total_customers
FROM subscriptions;

```
***
![]()

2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
***
```sql
SELECT
  TO_CHAR(TRUNC(start_date, 'MONTH'), 'YYYY-MM') AS month,
  COUNT(*) AS trial_plan_count
FROM subscriptions
WHERE plan_id = 0
GROUP BY TO_CHAR(TRUNC(start_date, 'MONTH'), 'YYYY-MM')
ORDER BY month;

```
***  
![]()
3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
***
```sql
SELECT
  p.plan_name,
  COUNT(*) AS events_count
FROM
  subscriptions s
JOIN
  plans p ON s.plan_id = p.plan_id
WHERE
  EXTRACT(YEAR FROM s.start_date) > 2020
GROUP BY
  p.plan_name
ORDER BY
  p.plan_name;
  
  

```
***  
![]()
 
4.What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
***
```sql
SELECT 
  SUM(CASE WHEN p.plan_name = 'churn' THEN 1 END) AS churn_count,
  ROUND(100 * SUM(CASE WHEN p.plan_name = 'churn' THEN 1 END) / COUNT(DISTINCT s.customer_id), 1) AS churn_pct
FROM subscriptions s
JOIN plans p 
  ON s.plan_id = p.plan_id;

```
***  
![]()
 
5.How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?

***
```sql

SELECT
  COUNT(*) AS "churn after trial count",
  ROUND(100 * COUNT(*) / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions WHERE plan_id = 0), 1) AS "churn percentage"
FROM (
  SELECT
    customer_id,
    plan_id,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY start_date) AS rank
  FROM
    subscriptions
) ranked_subs
WHERE plan_id = 4 AND rank = 2
GROUP BY 1;

```
***  
![]()

6.What is the number and percentage of customer plans after their initial free trial?
***
```sql
SELECT 
  s.plan_id,
  p.plan_name,
  COUNT(DISTINCT s.customer_id) AS "number of plans after trial",
  ROUND(100 * COUNT(DISTINCT s.customer_id) / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions WHERE plan_id = 0), 1) AS "percentage of plans after trial"
FROM subscriptions s
JOIN plans p ON s.plan_id = p.plan_id
WHERE p.plan_name != 'trial'
GROUP BY s.plan_id, p.plan_name
ORDER BY s.plan_id;

```
***  
![]()
7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
***
```sql

WITH latest_plan_cte AS (
  SELECT
    s.plan_id,
    p.plan_name,
    s.customer_id,
    ROW_NUMBER() OVER (PARTITION BY s.customer_id ORDER BY s.start_date DESC) AS latest_plan
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE s.start_date <= DATE '2020-12-31'
)
SELECT
  plan_id,
  plan_name,
  COUNT(DISTINCT customer_id) AS "customer count",
  ROUND(100 * COUNT(DISTINCT customer_id) / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions WHERE plan_id != 0), 2) AS "percentage_breakdown"
FROM latest_plan_cte
WHERE latest_plan = 1
GROUP BY plan_id, plan_name
ORDER BY plan_id;

```
***  
![]()
8.. How many customers have upgraded to an annual plan in 2020?
***
```sql
SELECT
  COUNT(DISTINCT customer_id) AS "number of customers"
FROM subscriptions s
JOIN plans p ON s.plan_id = p.plan_id
WHERE p.plan_name = 'pro annual'
  AND EXTRACT(YEAR FROM s.start_date) = 2020;

```
***  
![]()
9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
***
```sql

WITH trial_and_annual_dates AS (
  SELECT 
    s.customer_id,
    MIN(CASE WHEN p.plan_name = 'trial' THEN s.start_date END) AS trial_date,
    MIN(CASE WHEN p.plan_name = 'pro annual' THEN s.start_date END) AS annual_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name IN ('trial', 'pro annual')
  GROUP BY s.customer_id
)

SELECT 
  ROUND(AVG(CAST((annual_date - trial_date) AS FLOAT)),2) AS "average days to upgrade"
FROM trial_and_annual_dates;

```
***  
![]()
10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
***
```sql

WITH join_date AS (
    SELECT customer_id, start_date 
    FROM subscriptions 
    WHERE plan_id = 0
),
pro_date AS (
    SELECT customer_id, start_date AS upgrade_date 
    FROM subscriptions 
    WHERE plan_id = 3
),
bins AS (
    SELECT WIDTH_BUCKET((upgrade_date - start_date), 0, 360, 12) AS avg_days_to_upgrade
    FROM join_date
    JOIN pro_date ON join_date.customer_id = pro_date.customer_id
)

SELECT ((avg_days_to_upgrade - 1) * 30 || '-' || (avg_days_to_upgrade) * 30) AS "30-day-range", COUNT(*)
FROM bins
GROUP BY avg_days_to_upgrade
ORDER BY avg_days_to_upgrade;

```
***  
![]()
11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
***
```sql
WITH nextPlan AS (
  SELECT 
    s.customer_id,
    s.start_date,
    p.plan_name,
    LEAD(p.plan_name) OVER(PARTITION BY s.customer_id ORDER BY s.start_date) AS next_plan
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT COUNT(*) AS pro_to_basic_monthly
FROM nextPlan
WHERE plan_name = 'pro monthly'
  AND next_plan = 'basic monthly'
  AND EXTRACT(YEAR FROM start_date) = 2020;

```
***  
![]()
