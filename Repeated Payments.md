### Question

Sometimes, payment transactions are repeated by accident; it could be due to user error, API failure, or a retry error that causes a credit card to be charged twice.

Using the `transactions` table, identify any payments made at the same merchant with the same credit card for the same amount within 10 minutes of each other. Count such repeated payments.

### Assumptions

- The first transaction of such payments should not be counted as a repeated payment. This means if there are two transactions performed by a merchant with the same credit card and for the same amount within 10 minutes, there will only be 1 repeated payment.

### Table: `transactions`

| Column Name            | Type       |
|------------------------|------------|
| `transaction_id`       | integer    |
| `merchant_id`          | integer    |
| `credit_card_id`       | integer    |
| `amount`               | integer    |
| `transaction_timestamp`| datetime   |

### Example Input

| `transaction_id` | `merchant_id` | `credit_card_id` | `amount` | `transaction_timestamp` |
|-------------------|---------------|------------------|----------|--------------------------|
| 1                 | 101           | 1                | 100      | 09/25/2022 12:00:00     |
| 2                 | 101           | 1                | 100      | 09/25/2022 12:08:00     |
| 3                 | 101           | 1                | 100      | 09/25/2022 12:28:00     |
| 4                 | 102           | 2                | 300      | 09/25/2022 12:00:00     |
| 6                 | 102           | 2                | 400      | 09/25/2022 14:00:00     |

### Example Output

| `payment_count` |
|-----------------|
| 1               |

### Explanation

- Within 10 minutes after Transaction 1, Transaction 2 is conducted at Merchant 101 using the same credit card for the same amount. This is the only instance of repeated payment in the given sample data.
- Transaction 3 is completed 20 minutes after Transaction 2 and 28 minutes after Transaction 1, so it does not meet the repeated payments' conditions.
- Transactions 4 and 6 have different amounts, so they are not considered repeated payments.

The dataset you are querying against may have different input and output - this is just an example.

---

### Thought Process

1. **Objective:** Identify and count payments at the same merchant, using the same credit card, and for the same amount, occurring within 10 minutes of a prior transaction.
2. **Approach:**
   - Extract the total minutes from `transaction_timestamp` to simplify time difference calculations.
   - Use `LEAD()` to fetch the next transaction's timestamp for the same `merchant_id`, `credit_card_id`, and `amount`.
   - Calculate the time difference (in minutes) between the current transaction and the next transaction.
   - Filter transactions where the time difference is less than or equal to 10 minutes.
   - Count the qualifying repeated payments.

---

### Query

```sql
WITH cte AS (
    SELECT * ,
           (EXTRACT(HOUR FROM transaction_timestamp) * 60) + EXTRACT(MINUTE FROM transaction_timestamp) AS total_minutes
    FROM transactions
    ORDER BY merchant_id, credit_card_id, transaction_timestamp
),

cte2 AS (
    SELECT *,
           LEAD(transaction_timestamp, 1) OVER (PARTITION BY merchant_id, credit_card_id, amount ORDER BY transaction_timestamp) AS next_txn
    FROM cte
),

cte3 AS (
    SELECT *,
           (EXTRACT(HOUR FROM next_txn) * 60) + EXTRACT(MINUTE FROM next_txn) AS total_minutes_next
    FROM cte2
),

cte4 AS (
    SELECT *,
           total_minutes_next - total_minutes AS within_mins
    FROM cte3
)

SELECT COUNT(*) AS payment_count
FROM cte4
WHERE within_mins <= 10;
