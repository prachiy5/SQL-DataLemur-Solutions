
### Problem Description

You're provided with two tables: the `advertiser` table contains information about advertisers and their respective payment statuses, and the `daily_pay` table contains the current payment information for advertisers (it only includes advertisers who have made payments).

Write a query to update the payment status of Facebook advertisers based on the information in the `daily_pay` table. The output should include the `user_id` and their current payment status, sorted by `user_id`.

### Payment Status Transitions:
The payment status of advertisers can be classified into the following categories:

1. **New**: Advertisers who are newly registered and have made their first payment.
2. **Existing**: Advertisers who have made payments in the past and have recently made a current payment.
3. **Churn**: Advertisers who have made payments in the past but have not made any recent payment.
4. **Resurrect**: Advertisers who have not made a recent payment but may have made a previous payment and have made a payment again recently.

#### Status Transition Rules:
| Current Status | Updated Status | Payment on Day T |
|----------------|----------------|-------------------|
| NEW            | EXISTING       | Paid              |
| NEW            | CHURN          | Not paid          |
| EXISTING       | EXISTING       | Paid              |
| EXISTING       | CHURN          | Not paid          |
| CHURN          | RESURRECT      | Paid              |
| CHURN          | CHURN          | Not paid          |
| RESURRECT      | EXISTING       | Paid              |
| RESURRECT      | CHURN          | Not paid          |

### Table Schemas:

#### `advertiser` Table:
| Column Name | Type   | Description                          |
|-------------|--------|--------------------------------------|
| `user_id`   | string | Unique identifier for the advertiser.|
| `status`    | string | Current payment status of advertiser.|

**Example Input:**
| `user_id` | `status` |
|-----------|----------|
| bing      | NEW      |
| yahoo     | NEW      |
| alibaba   | EXISTING |

#### `daily_pay` Table:
| Column Name | Type   | Description                               |
|-------------|--------|-------------------------------------------|
| `user_id`   | string | Unique identifier for the advertiser.     |
| `paid`      | decimal| Payment amount made by the advertiser.    |

**Example Input:**
| `user_id` | `paid` |
|-----------|-------|
| yahoo     | 45.00 |
| alibaba   | 100.00|
| target    | 13.00 |

### Expected Output:
| `user_id` | `new_status` |
|-----------|--------------|
| bing      | CHURN        |
| yahoo     | EXISTING     |
| alibaba   | EXISTING     |

### Explanation:
- The advertiser `bing` is classified as `CHURN` because no payment record is found in the `daily_pay` table.
- The advertiser `yahoo` is labeled as `EXISTING` since they have made a recent payment, as indicated by the presence of their payment record.
- Similarly, the advertiser `alibaba` is also classified as `EXISTING` due to their recent payment.

### Thought Process:
1. **Identify Missing Payments:**
   - Join the `advertiser` table with the `daily_pay` table on `user_id`.
   - Advertisers with no payment in the `daily_pay` table are classified as `CHURN`.

2. **Check Current Status:**
   - For advertisers who have made payments, check their current status in the `advertiser` table.
   - Update their status based on the rules specified above.

3. **Handle New Advertisers:**
   - Advertisers present in the `daily_pay` table but not in the `advertiser` table are classified as `NEW`.

4. **Output the Results:**
   - Include `user_id` and the updated status (`new_status`) and sort the results by `user_id`.

---

### Query:
```sql
SELECT user_id,
       CASE 
           WHEN paid IS NULL THEN 'CHURN' 
           WHEN paid IS NOT NULL AND status IN ('NEW', 'EXISTING', 'RESURRECT') THEN 'EXISTING'
           WHEN paid IS NOT NULL AND status = 'CHURN' THEN 'RESURRECT'
           WHEN paid IS NOT NULL AND status IS NULL THEN 'NEW'
       END AS new_status
FROM advertiser a
FULL OUTER JOIN daily_pay dp USING (user_id)
ORDER BY user_id;
