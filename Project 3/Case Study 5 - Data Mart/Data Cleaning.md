*** 
```sql
CREATE TABLE weekly_sales_clean AS
SELECT
    TO_DATE(week_date, 'DD-MM-YYYY') AS week_date,
    CEIL(EXTRACT(DAY FROM TO_DATE(week_date, 'DD-MM-YYYY')) / 7) AS week_number,
    EXTRACT(MONTH FROM TO_DATE(week_date, 'DD-MM-YYYY')) AS month_number,
    EXTRACT(YEAR FROM TO_DATE(week_date, 'DD-MM-YYYY')) AS calendar_year,
    CASE
        WHEN SUBSTR(segment, -1) = '1' THEN 'Young Adults'
        WHEN SUBSTR(segment, -1) = '2' THEN 'Middle Aged'
        WHEN SUBSTR(segment, -1) IN ('3', '4') THEN 'Retirees'
        ELSE 'unknown'
    END AS age_band,
    CASE
        WHEN SUBSTR(segment, 1, 1) = 'C' THEN 'Couples'
        WHEN SUBSTR(segment, 1, 1) = 'F' THEN 'Families'
        ELSE 'unknown'
    END AS demographic,
    ROUND(sales / NULLIF(transactions, 0), 2) AS avg_transaction
FROM weekly_sales
WHERE segment IS NOT NULL;



```
***

![]()
