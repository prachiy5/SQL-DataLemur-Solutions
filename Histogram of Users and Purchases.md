### Problem Description:
This is the same question as problem #13 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given a table on Walmart user transactions. Based on their most recent transaction date, write a query that retrieves the users along with the number of products they bought.

Output the user's most recent transaction date, user ID, and the number of products, sorted in chronological order by the transaction date.

---

#### `user_transactions` Table:
| Column Name       | Type      |
|-------------------|-----------|
| `product_id`      | integer   |
| `user_id`         | integer   |
| `spend`           | decimal   |
| `transaction_date`| timestamp |

---

#### Example Input:
| `product_id` | `user_id` | `spend` | `transaction_date`       |
|--------------|-----------|---------|--------------------------|
| 3673         | 123       | 68.90   | 07/08/2022 12:00:00      |
| 9623         | 123       | 274.10  | 07/08/2022 12:00:00      |
| 1467         | 115       | 19.90   | 07/08/2022 12:00:00      |
| 2513         | 159       | 25.00   | 07/08/2022 12:00:00      |
| 1452         | 159       | 74.50   | 07/10/2022 12:00:00      |

---

#### Example Output:
| `transaction_date`      | `user_id` | `purchase_count` |
|--------------------------|-----------|------------------|
| 07/08/2022 12:00:00     | 115       | 1                |
| 07/08/2022 12:00:00     | 123       | 2                |
| 07/10/2022 12:00:00     | 159       | 1                |

**Explanation:**
- For **user 123**, their most recent transaction was on **07/08/2022**, and they purchased 2 products.
- For **user 115**, their most recent transaction was also on **07/08/2022**, and they purchased 1 product.
- For **user 159**, their most recent transaction was on **07/10/2022**, and they purchased 1 product.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Identify the Most Recent Transaction per User:**
   - Use the `RANK()` window function to rank transactions for each user based on their transaction date in descending order.
2. **Filter to Keep Only the Most Recent Transaction:**
   - Restrict the results to rows where the rank is equal to 1, indicating the most recent transaction for each user.
3. **Count Products per Transaction:**
   - For each most recent transaction, count the number of products purchased (`product_id`).
4. **Sort Results Chronologically:**
   - Order the results by `transaction_date` to present the data in chronological order.

---

### Query:
```sql
WITH latest_transactions_cte AS (
  SELECT 
    transaction_date, 
    user_id, 
    product_id, 
    RANK() OVER (
      PARTITION BY user_id 
      ORDER BY transaction_date DESC) AS transaction_rank 
  FROM user_transactions
) 
  
SELECT 
  transaction_date, 
  user_id,
  COUNT(product_id) AS purchase_count
FROM latest_transactions_cte
WHERE transaction_rank = 1 
GROUP BY transaction_date, user_id
ORDER BY transaction_date;
