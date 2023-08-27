# SQL Case Study - Foodie-Fi by Danny Ma


### A. Customer Journey
Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.
```TSQL
SELECT 
	s.*,
	p.plan_name,
	p.price
FROM subscriptions s
JOIN plans p ON s.plan_id = p.plan_id
WHERE customer_id IN (1, 2, 11, 13, 15, 16, 18, 19);
```
| customer_id | plan_id | start_date | plan_name     | price   |
|-------------|---------|------------|---------------|---------|
| 1           | 0       | 2020-08-01 | trial         | 0.00    |
| 1           | 1       | 2020-08-08 | basic monthly | 9.90    |
| 2           | 0       | 2020-09-20 | trial         | 0.00    |
| 2           | 3       | 2020-09-27 | pro annual    | 199.00  |
| 11          | 0       | 2020-11-19 | trial         | 0.00    |
| 11          | 4       | 2020-11-26 | churn         | NULL    |
| 13          | 0       | 2020-12-15 | trial         | 0.00    |
| 13          | 1       | 2020-12-22 | basic monthly | 9.90    |
| 13          | 2       | 2021-03-29 | pro monthly   | 19.90   |
| 15          | 0       | 2020-03-17 | trial         | 0.00    |
| 15          | 2       | 2020-03-24 | pro monthly   | 19.90   |
| 15          | 4       | 2020-04-29 | churn         | NULL    |
| 16          | 0       | 2020-05-31 | trial         | 0.00    |
| 16          | 1       | 2020-06-07 | basic monthly | 9.90    |
| 16          | 3       | 2020-10-21 | pro annual    | 199.00  |
| 18          | 0       | 2020-07-06 | trial         | 0.00    |
| 18          | 2       | 2020-07-13 | pro monthly   | 19.90   |
| 19          | 0       | 2020-06-22 | trial         | 0.00    |
| 19          | 2       | 2020-06-29 | pro monthly   | 19.90   |


## B. Data Analysis Questions
### 1. How many customers has Foodie-Fi ever had?
```TSQL
SELECT COUNT(DISTINCT customer_id) AS unique_customers
FROM subscriptions;
```
| unique_customers  |
|-------------------|
| 1000              |

---
### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?
```TSQL
SELECT 
  MONTH(s.start_date) AS months,
  COUNT(*) AS distribution_values
FROM subscriptions s
JOIN plans p 
  ON s.plan_id = p.plan_id
WHERE p.plan_name = 'trial'
GROUP BY MONTH(s.start_date);
```
| months | distribution_values  |
|--------|----------------------|
| 1      | 88                   |
| 2      | 68                   |
| 3      | 94                   |
| 4      | 81                   |
| 5      | 88                   |
| 6      | 79                   |
| 7      | 89                   |
| 8      | 88                   |
| 9      | 87                   |
| 10     | 79                   |
| 11     | 75                   |
| 12     | 84                   |

---
### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name?
```TSQL
SELECT 
  YEAR(s.start_date) AS events,
  p.plan_name,
  COUNT(*) AS counts
FROM subscriptions s
JOIN plans p 
  ON s.plan_id = p.plan_id
WHERE YEAR(s.start_date) > 2020
GROUP BY YEAR(s.start_date), p.plan_name;
```
| events | plan_name     | counts  |
|--------|---------------|---------|
| 2021   | basic monthly | 8       |
| 2021   | churn         | 71      |
| 2021   | pro annual    | 63      |
| 2021   | pro monthly   | 60      |

---
### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```TSQL
SELECT 
  SUM(CASE WHEN p.plan_name = 'churn' THEN 1 END) AS churn_count,
  CAST(100*SUM(CASE WHEN p.plan_name = 'churn' THEN 1 END) AS FLOAT(1)) 
    / COUNT(DISTINCT customer_id) AS churn_pct
FROM subscriptions s
JOIN plans p 
  ON s.plan_id = p.plan_id;
```
| churn_count | churn_pct   |
|-------------|-------------|
| 307         | 30.7        |

---
### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
```TSQL
WITH nextPlan AS (
  SELECT 
    s.customer_id,
    s.start_date,
    p.plan_name,
    LEAD(p.plan_name) OVER(PARTITION BY s.customer_id ORDER BY p.plan_id) AS next_plan
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT 
  COUNT(*) AS churn_after_trial,
  100*COUNT(*) / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions) AS pct
FROM nextPlan
WHERE plan_name = 'trial' 
  AND next_plan = 'churn';
```
| churn_after_trial | pct         |
|-------------------|-------------|
| 92                | 9           |

---
### 6. What is the number and percentage of customer plans after their initial free trial?
```TSQL
WITH nextPlan AS (
  SELECT 
    s.customer_id,
    s.start_date,
    p.plan_name,
    LEAD(p.plan_name) OVER(PARTITION BY s.customer_id 
			ORDER BY p.plan_id) AS next_plan
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT 
  next_plan,
  COUNT(*) AS customer_plan,
  CAST(100 * COUNT(*) AS FLOAT) 
      / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions) AS percentage
FROM nextPlan
WHERE next_plan IS NOT NULL
  AND plan_name = 'trial'
GROUP BY next_plan;
```
| next_plan     | customer_plan | percentage  |
|---------------|---------------|-------------|
| basic monthly | 546           | 54.6        |
| churn         | 92            | 9.2         |
| pro annual    | 37            | 3.7         |
| pro monthly   | 325           | 32.5        |

---
### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
```TSQL
WITH plansDate AS (
  SELECT 
    s.customer_id,
    s.start_date,
	p.plan_id,
    p.plan_name,
    LEAD(s.start_date) OVER(PARTITION BY s.customer_id ORDER BY s.start_date) AS next_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT 
  plan_id,
  plan_name,
  COUNT(*) AS customers,
  CAST(100*COUNT(*) AS FLOAT) 
      / (SELECT COUNT(DISTINCT customer_id) FROM subscriptions) AS conversion_rate
FROM plansDate
WHERE (next_date IS NOT NULL AND (start_date < '2020-12-31' AND next_date > '2020-12-31'))
  OR (next_date IS NULL AND start_date < '2020-12-31')
GROUP BY plan_id, plan_name
ORDER BY plan_id;
```
| plan_id | plan_name     | customers | conversion_rate  |
|---------|---------------|-----------|------------------|
| 0       | trial         | 19        | 1.9              |
| 1       | basic monthly | 224       | 22.4             |
| 2       | pro monthly   | 326       | 32.6             |
| 3       | pro annual    | 195       | 19.5             |
| 4       | churn         | 235       | 23.5             |

---
### 8. How many customers have upgraded to an annual plan in 2020?
```TSQL
SELECT 
  COUNT(DISTINCT customer_id) AS customer_count
FROM subscriptions s
JOIN plans p ON s.plan_id = p.plan_id
WHERE p.plan_name = 'pro annual'
  AND YEAR(s.start_date) = 2020;
```
| customer_count  |
|-----------------|
| 195             |

---
### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
```TSQL
WITH trialPlan AS (
  SELECT 
    s.customer_id,
    s.start_date AS trial_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name = 'trial'
),
annualPlan AS (
  SELECT 
    s.customer_id,
    s.start_date AS annual_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name = 'pro annual'
)

SELECT 
  AVG(CAST(DATEDIFF(d, trial_date, annual_date) AS FLOAT)) AS avg_days_to_annual
FROM trialPlan t
JOIN annualPlan a 
ON t.customer_id = a.customer_id;
```
| avg_days_to_annual  |
|---------------------|
| 104.62015503876     |

On average, it takes 105 days for a customer to an annual plan from the day they join Foodie-Fi.

---
### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)?
To solve this question: 
* Utilize 2 CTEs in the previous question: ```trialPlan``` and ```annualPlan``` to calculate the number of days between ```trial_date``` and ```annual_date```, then put that to new CTE named ```datesDiff```
* Create a recursive CTE named ```daysRecursion``` to generate 30-day periods (i.e. 0-30 days, 31-60 days etc)
* Left join from ```daysRecursion``` to ```datesDiff``` 
    
```TSQL
WITH trialPlan AS (
  SELECT 
    s.customer_id,
    s.start_date AS trial_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name = 'trial'
),
annualPlan AS (
  SELECT 
    s.customer_id,
    s.start_date AS annual_date
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  WHERE p.plan_name = 'pro annual'
),
datesDiff AS (
  SELECT 
    t.customer_id,
    DATEDIFF(d, trial_date, annual_date) AS diff
  FROM trialPlan t
  JOIN annualPlan a ON t.customer_id = a.customer_id
),
daysRecursion AS (
  SELECT 
    0 AS start_period, 
    30 AS end_period
  UNION ALL
  SELECT 
    end_period + 1 AS start_period,
    end_period + 30 AS end_period
  FROM daysRecursion
  WHERE end_period < 360
)

SELECT 
  dr.start_period,
  dr.end_period,
  COUNT(*) AS customer_count
FROM daysRecursion dr
LEFT JOIN datesDiff dd 
  ON (dd.diff >= dr.start_period AND dd.diff <= dr.end_period)
GROUP BY dr.start_period, dr.end_period;
```
| start_period | end_period | customer_count  |
|--------------|------------|-----------------|
| 0            | 30         | 49              |
| 31           | 60         | 24              |
| 61           | 90         | 34              |
| 91           | 120        | 35              |
| 121          | 150        | 42              |
| 151          | 180        | 36              |
| 181          | 210        | 26              |
| 211          | 240        | 4               |
| 241          | 270        | 5               |
| 271          | 300        | 1               |
| 301          | 330        | 1               |
| 331          | 360        | 1               |

---
### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
```TSQL
WITH nextPlan AS (
  SELECT 
    s.customer_id,
    s.start_date,
    p.plan_name,
    LEAD(p.plan_name) OVER(PARTITION BY s.customer_id ORDER BY p.plan_id) AS next_plan
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
)

SELECT COUNT(*) AS pro_to_basic_monthly
FROM nextPlan
WHERE plan_name = 'pro monthly'
  AND next_plan = 'basic monthly'
  AND YEAR(start_date) = 2020;
```
| pro_to_basic_monthly|
|---------------------|
| 0                   |

There were no customers downgrading from a pro monthly to a basic monthly plan in 2020.

---

## C. Challenge Payment Question
The Foodie-Fi team wants to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:
  * monthly payments always occur on the same day of month as the original start_date of any monthly paid plan
  * upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately
  * upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period
  * once a customer churns they will no longer make payments

Example outputs for this table might look like the following:

| customer_id | plan_id | plan_name     | payment_date | amount | payment_order  |
|-------------|---------|---------------|--------------|--------|----------------|
| 1           | 1       | basic monthly | 2020-08-08   | 9.90   | 1              |
| 1           | 1       | basic monthly | 2020-09-08   | 9.90   | 2              |
| 1           | 1       | basic monthly | 2020-10-08   | 9.90   | 3              |
| 1           | 1       | basic monthly | 2020-11-08   | 9.90   | 4              |
| 1           | 1       | basic monthly | 2020-12-08   | 9.90   | 5              |
| 2           | 3       | pro annual    | 2020-09-27   | 199.00 | 1              |
| 13          | 1       | basic monthly | 2020-12-22   | 9.90   | 1              |
| 15          | 2       | pro monthly   | 2020-03-24   | 19.90  | 1              |
| 15          | 2       | pro monthly   | 2020-04-24   | 19.90  | 2              |
| 16          | 1       | basic monthly | 2020-06-07   | 9.90   | 1              |
| 16          | 1       | basic monthly | 2020-07-07   | 9.90   | 2              |
| 16          | 1       | basic monthly | 2020-08-07   | 9.90   | 3              |
| 16          | 1       | basic monthly | 2020-09-07   | 9.90   | 4              |
| 16          | 1       | basic monthly | 2020-10-07   | 9.90   | 5              |
| 16          | 3       | pro annual    | 2020-10-21   | 189.10 | 6              |
| 18          | 2       | pro monthly   | 2020-07-13   | 19.90  | 1              |
| 18          | 2       | pro monthly   | 2020-08-13   | 19.90  | 2              |
| 18          | 2       | pro monthly   | 2020-09-13   | 19.90  | 3              |
| 18          | 2       | pro monthly   | 2020-10-13   | 19.90  | 4              |

---
  * Use a recursive CTE to increment rows for all monthly paid plans in 2020 until customers changing their plans, except 'pro annual'.
    * use ```CASE``` to create a new column ```last_date```: last day of the current plan
    * if a customer kept using the current plan, last_date = '2020-12-31'
    * if a customer changed the plan, last_date = (month difference between start_date and changing date) + start_date
  * Create a new table ```payments``` by selecting the required columns

```TSQL
--Use a recursive CTE to increment rows for all monthly paid plans until customers changing the plan, except 'pro annual'
WITH dateRecursion AS (
  SELECT 
    s.customer_id,
    s.plan_id,
    p.plan_name,
    s.start_date AS payment_date,
    --column last_date: last day of the current plan
    CASE 
      --if a customer kept using the current plan, last_date = '2020-12-31'
      WHEN LEAD(s.start_date) OVER(PARTITION BY s.customer_id ORDER BY s.start_date) IS NULL THEN '2020-12-31'
      --if a customer changed the plan, last_date = (month difference between start_date and changing date) + start_date
      ELSE DATEADD(MONTH, 
		   DATEDIFF(MONTH, start_date, LEAD(s.start_date) OVER(PARTITION BY s.customer_id ORDER BY s.start_date)),
		   start_date) END AS last_date,
    p.price AS amount
  FROM subscriptions s
  JOIN plans p ON s.plan_id = p.plan_id
  --exclude trials because they didn't generate payments 
  WHERE p.plan_name NOT IN ('trial')
    AND YEAR(start_date) = 2020

  UNION ALL

  SELECT 
    customer_id,
    plan_id,
    plan_name,
    --increment payment_date by monthly
    DATEADD(MONTH, 1, payment_date) AS payment_date,
    last_date,
    amount
  FROM dateRecursion
  --stop incrementing when payment_date = last_date
  WHERE DATEADD(MONTH, 1, payment_date) <= last_date
    AND plan_name != 'pro annual'
)
--Create a new table [payments]
SELECT 
  customer_id,
  plan_id,
  plan_name,
  payment_date,
  amount,
  ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY payment_date) AS payment_order
INTO payments
FROM dateRecursion
--exclude churns
WHERE amount IS NOT NULL
ORDER BY customer_id
OPTION (MAXRECURSION 365);
```
---
