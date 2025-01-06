---
### Problem Description:
This is the same question as problem #32 in the SQL Chapter of *Ace the Data Science Interview*!

Assume you're given a table containing information about Wayfair user transactions for different products. Write a query to calculate the year-on-year growth rate for the total spend of each product, grouping the results by product ID.

The output should include:
- The year in ascending order
- Product ID
- Current year's spend
- Previous year's spend
- Year-on-year growth percentage, rounded to 2 decimal places.

#### `user_transactions` Table:
| Column Name       | Type      | Description                                   |
|-------------------|-----------|-----------------------------------------------|
| `transaction_id`  | integer   | The unique ID of the transaction.            |
| `product_id`      | integer   | The ID of the product purchased.             |
| `spend`           | decimal   | The amount spent on the transaction.         |
| `transaction_date`| datetime  | The date the transaction took place.         |

#### Example Input:
| `transaction_id` | `product_id` | `spend`  | `transaction_date`       |
|------------------|--------------|----------|--------------------------|
| 1341            | 123424       | 1500.60  | 12/31/2019 12:00:00      |
| 1423            | 123424       | 1000.20  | 12/31/2020 12:00:00      |
| 1623            | 123424       | 1246.44  | 12/31/2021 12:00:00      |
| 1322            | 123424       | 2145.32  | 12/31/2022 12:00:00      |

#### Example Output:
| `year` | `product_id` | `curr_year_spend` | `prev_year_spend` | `yoy_rate` |
|--------|--------------|-------------------|-------------------|------------|
| 2019   | 123424       | 1500.60           | NULL              | NULL       |
| 2020   | 123424       | 1000.20           | 1500.60           | -33.35     |
| 2021   | 123424       | 1246.44           | 1000.20           | 24.62      |
| 2022   | 123424       | 2145.32           | 1246.44           | 72.12      |

---

### Thought Process:
1. **Group Transactions by Year and Product:** Use the `EXTRACT(YEAR FROM transaction_date)` function to group the total spend (`SUM(spend)`) for each product by year.
2. **Identify the Previous Year's Spend:** Use the `LAG` window function to retrieve the spend from the previous year for each product.
3. **Calculate Year-on-Year Growth:** Use a `CASE` statement to calculate the percentage change in spend from the previous year. Handle cases where the previous year's spend is `NULL` or zero to avoid division errors.
4. **Format Results:** Round the year-on-year growth percentage to 2 decimal places and ensure the results are sorted by product ID and year.

---

### Query:
```sql
WITH cte AS (
    SELECT 
        product_id,
        EXTRACT(YEAR FROM transaction_date) AS year,
        SUM(spend) AS curr_year_spend
    FROM user_transactions
    GROUP BY product_id, year
),
cte2 AS (
    SELECT 
        product_id,
        year,
        curr_year_spend,
        LAG(curr_year_spend) OVER (PARTITION BY product_id ORDER BY year) AS prev_year_spend
    FROM cte
)
SELECT 
    year,
    product_id,
    curr_year_spend,
    prev_year_spend,
    ROUND(
        CASE 
            WHEN prev_year_spend IS NULL OR prev_year_spend = 0 THEN NULL
            ELSE ((curr_year_spend - prev_year_spend) / prev_year_spend) * 100.0
        END, 
        2
    ) AS yoy_rate
FROM cte2
ORDER BY product_id, year;
```

---

### Explanation of the Query:
1. **CTE Step 1 (`cte`):**
   - Calculate the total spend (`SUM(spend)`) for each product in each year.
   - Extract the year from the transaction date using `EXTRACT(YEAR FROM transaction_date)`.
2. **CTE Step 2 (`cte2`):**
   - Use the `LAG` function to retrieve the spend from the previous year (`prev_year_spend`) for each product.
   - Ensure the spend is ordered by year within each product.
3. **Final Query:**
   - Calculate the Year-on-Year growth rate as `((curr_year_spend - prev_year_spend) / prev_year_spend) * 100`.
   - Handle cases where `prev_year_spend` is `NULL` or zero to avoid division errors.
   - Round the growth rate to 2 decimal places for readability.
   - Sort the results by `product_id` and `year`.

---
