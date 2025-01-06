### Problem Description:
New TikTok users sign up with their emails and confirm their signup by replying to the text confirmation to activate their accounts. Users may receive multiple text messages for account confirmation until they confirm their new account.

A senior analyst is interested in knowing the activation rate of specified users in the `emails` table. Write a query to calculate the activation rate and round the percentage to 2 decimal places.

#### Definitions:
- **Activation Rate:** The percentage of users who successfully confirmed their signup via text.
- `emails` table contains the information of user signup details.
- `texts` table contains the users' activation information.

#### Assumptions:
1. The analyst is interested in the activation rate of specific users in the `emails` table, which may not include all users that could potentially be found in the `texts` table.
2. For example, user 123 in the `emails` table may not be in the `texts` table and vice versa.
3. Effective April 4th, 2023, this assumption was added to provide additional clarity.

---

#### `emails` Table:
| Column Name   | Type      |
|---------------|-----------|
| `email_id`    | integer   |
| `user_id`     | integer   |
| `signup_date` | datetime  |

#### Example Input:
| `email_id` | `user_id` | `signup_date`         |
|------------|-----------|-----------------------|
| 125        | 7771      | 06/14/2022 00:00:00  |
| 236        | 6950      | 07/01/2022 00:00:00  |
| 433        | 1052      | 07/09/2022 00:00:00  |

---

#### `texts` Table:
| Column Name      | Type    |
|------------------|---------|
| `text_id`        | integer |
| `email_id`       | integer |
| `signup_action`  | varchar |

#### Example Input:
| `text_id` | `email_id` | `signup_action` |
|-----------|------------|-----------------|
| 6878      | 125        | Confirmed       |
| 6920      | 236        | Not Confirmed   |
| 6994      | 236        | Confirmed       |

---

#### Example Output:
| `confirm_rate` |
|----------------|
| 0.67           |

**Explanation:**
- Out of 3 users in the `emails` table:
  - User 125 successfully confirmed their account.
  - User 236 also confirmed their account despite receiving multiple text messages.
  - User 433 did not confirm their account (not present in the `texts` table).
- Activation Rate = \( \frac{2}{3} = 0.67 \) or 67%.

---

### Thought Process:
1. **Identify Confirmed Users:** Filter the `texts` table for rows where `signup_action = 'Confirmed'`.
2. **Join Tables:** Perform a `LEFT JOIN` between the `emails` table and the filtered `texts` table to account for all users in the `emails` table.
3. **Count Total Users:** Use `COUNT(DISTINCT email_id)` to get the total number of users in the `emails` table.
4. **Count Confirmed Users:** Use `COUNT(email_id)` on the joined data to calculate the number of confirmed users.
5. **Calculate Activation Rate:** Divide the number of confirmed users by the total number of users and round the result to 2 decimal places.

---

### Query:
```sql
SELECT 
  ROUND(COUNT(t.email_id)::decimal / COUNT(DISTINCT e.email_id), 2) AS confirm_rate
FROM emails e
LEFT JOIN texts t 
  ON e.email_id = t.email_id
  AND t.signup_action = 'Confirmed';
