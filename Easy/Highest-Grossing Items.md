### Problem Description:
This is the same question as problem #12 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given a table containing data on Amazon customers and their spending on products in different categories. Write a query to identify the top two highest-grossing products within each category in the year 2022. The output should include the `category`, `product`, and `total_spend`.

---

#### `product_spend` Table:
| Column Name        | Type      |
|--------------------|-----------|
| `category`         | string    |
| `product`          | string    |
| `user_id`          | integer   |
| `spend`            | decimal   |
| `transaction_date` | timestamp |

#### Example Input:
| `category`    | `product`          | `user_id` | `spend` | `transaction_date`      |
|---------------|--------------------|-----------|---------|-------------------------|
| appliance     | refrigerator       | 165       | 246.00  | 12/26/2021 12:00:00    |
| appliance     | refrigerator       | 123       | 299.99  | 03/02/2022 12:00:00    |
| appliance     | washing machine    | 123       | 219.80  | 03/02/2022 12:00:00    |
| electronics   | vacuum             | 178       | 152.00  | 04/05/2022 12:00:00    |
| electronics   | wireless headset   | 156       | 249.90  | 07/08/2022 12:00:00    |
| electronics   | vacuum             | 145       | 189.00  | 07/15/2022 12:00:00    |

#### Example Output:
| `category`    | `product`          | `total_spend` |
|---------------|--------------------|---------------|
| appliance     | refrigerator       | 299.99        |
| appliance     | washing machine    | 219.80        |
| electronics   | vacuum             | 341.00        |
| electronics   | wireless headset   | 249.90        |

**Explanation:**
- Within the "appliance" category, the top two highest-grossing products are "refrigerator" and "washing machine."
- In the "electronics" category, the top two highest-grossing products are "vacuum" and "wireless headset."

The dataset you are querying against may have different input & output - this is just an example.

---

### Thought Process:
1. **Filter Data for 2022:** Use the `WHERE` clause to limit the data to transactions made in 2022.
2. **Aggregate Spending by Product:** Use `SUM(spend)` to calculate the total spending for each product within each category.
3. **Rank Products by Spending:** Use the `RANK()` window function to rank products within each category based on their total spending in descending order.
4. **Filter Top 2 Products per Category:** Use a `WHERE` clause to keep only the top two products (based on rank) for each category.
5. **Order the Results:** Sort the output by `category` and `ranking` for readability.

---

### Query:
```sql
WITH ranked_spending_cte AS (
  SELECT 
    category, 
    product, 
    SUM(spend) AS total_spend,
    RANK() OVER (
      PARTITION BY category 
      ORDER BY SUM(spend) DESC) AS ranking 
  FROM product_spend
  WHERE EXTRACT(YEAR FROM transaction_date) = 2022
  GROUP BY category, product
)

SELECT 
  category, 
  product, 
  total_spend 
FROM ranked_spending_cte 
WHERE ranking <= 2 
ORDER BY category, ranking;
