# Customer Journey

Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

*** 
```sql

SELECT 
	s.*,
	p.plan_name,
	p.price
FROM subscriptions s
JOIN plans p ON s.plan_id = p.plan_id
WHERE customer_id IN (1, 2, 11, 13, 15, 16, 18, 19);


```
***

![]()
