### Problem Description:
In an effort to identify high-value customers, Amazon asked for your help to obtain data about users who go on shopping sprees. A shopping spree occurs when a user makes purchases on **3 or more consecutive days**.

List the user IDs who have gone on at least 1 shopping spree in ascending order.

---

#### `transactions` Table:
| Column Name       | Type      |
|-------------------|-----------|
| `user_id`         | integer   |
| `amount`          | float     |
| `transaction_date`| timestamp |

#### Example Input:
| `user_id` | `amount` | `transaction_date`       |
|-----------|----------|--------------------------|
| 1         | 9.99     | 08/01/2022 10:00:00     |
| 1         | 55.00    | 08/17/2022 10:00:00     |
| 2         | 149.50   | 08/05/2022 10:00:00     |
| 2         | 4.89     | 08/06/2022 10:00:00     |
| 2         | 34.00    | 08/07/2022 10:00:00     |

---

#### Example Output:
| `user_id` |
|-----------|
| 2         |

---

#### Explanation:
- In this example, `user_id` 2 is the only one who has gone on a shopping spree because they made purchases on **3 consecutive days** (08/05, 08/06, and 08/07).
- `user_id` 1 did not make purchases on consecutive days and is therefore excluded.

The dataset you are querying against may have different input & output - this is just an example!

---

### Thought Process:
1. **Identify Consecutive Transactions:**
   - Use a window function (`LEAD`) to calculate the difference in days between consecutive transactions for each user.
   - Extract the number of days between the current and next transaction date.

2. **Filter Consecutive Days:**
   - Identify transactions where the difference in days between consecutive purchases is 1 (indicating consecutive days).

3. **Group Consecutive Streaks:**
   - To determine if a user has a streak of 3 or more consecutive days, count the number of consecutive day differences grouped by the user.

4. **Output Unique User IDs:**
   - Return the user IDs of those who have at least 1 streak of 3 consecutive purchases.

---

### Query:
```sql
WITH cte AS (
    SELECT 
        user_id,
        transaction_date,
        LEAD(transaction_date) OVER (PARTITION BY user_id ORDER BY transaction_date) AS next_transaction_date,
        EXTRACT(DAY FROM (LEAD(transaction_date) OVER (PARTITION BY user_id ORDER BY transaction_date) - transaction_date)) AS days_difference
    FROM transactions
)
SELECT DISTINCT user_id
FROM cte
WHERE days_difference = 1;
