### Problem Description:
This is the same question as problem #11 in the SQL Chapter of Ace the Data Science Interview!

Assume you are given the table below on Uber transactions made by users. Write a query to obtain the third transaction of every user. Output the `user_id`, `spend`, and `transaction_date`.

#### `transactions` Table:
| Column Name       | Type      |
|-------------------|-----------|
| `user_id`         | integer   |
| `spend`           | decimal   |
| `transaction_date`| timestamp |

#### Example Input:
| `user_id` | `spend` | `transaction_date`     |
|-----------|---------|------------------------|
| 111       | 100.50  | 01/08/2022 12:00:00   |
| 111       | 55.00   | 01/10/2022 12:00:00   |
| 121       | 36.00   | 01/18/2022 12:00:00   |
| 145       | 24.99   | 01/26/2022 12:00:00   |
| 111       | 89.60   | 02/05/2022 12:00:00   |

#### Example Output:
| `user_id` | `spend` | `transaction_date`     |
|-----------|---------|------------------------|
| 111       | 89.60   | 02/05/2022 12:00:00   |

**Explanation:**
- For `user_id` 111, the third transaction occurred on `02/05/2022` with a spend of `89.60`.
- The query outputs the third transaction for every user.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Rank Transactions by User:** Use the `ROW_NUMBER()` window function to assign a rank to each transaction for every user based on the `transaction_date` in ascending order.
2. **Filter for Third Transaction:** Use a `WITH` clause to create a temporary table containing ranked transactions and filter where the rank (`row_num`) is equal to 3.
3. **Output Required Columns:** Select the `user_id`, `spend`, and `transaction_date` for the filtered rows.

---

### Query:
```sql
WITH third_user AS (
  SELECT 
    user_id, 
    spend, 
    transaction_date,
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY transaction_date) AS row_num
  FROM transactions
)

SELECT 
  user_id, 
  spend, 
  transaction_date
FROM third_user 
WHERE row_num = 3;
