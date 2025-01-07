### Problem Description:
Assume you're given tables with information about TikTok user sign-ups and confirmations through email and text. New users on TikTok sign up using their email addresses, and upon sign-up, each user receives a text message confirmation to activate their account.

Write a query to display the user IDs of those who did not confirm their sign-up on the first day but confirmed on the second day.

#### Definition:
- `action_date` refers to the date when users activated their accounts and confirmed their sign-up through text messages.

---

#### `emails` Table:
| Column Name   | Type       |
|---------------|------------|
| `email_id`    | integer    |
| `user_id`     | integer    |
| `signup_date` | datetime   |

#### Example Input:
| `email_id` | `user_id` | `signup_date`        |
|------------|-----------|----------------------|
| 125        | 7771      | 06/14/2022 00:00:00 |
| 433        | 1052      | 07/09/2022 00:00:00 |

#### `texts` Table:
| Column Name    | Type       |
|----------------|------------|
| `text_id`      | integer    |
| `email_id`     | integer    |
| `signup_action`| string ('Confirmed', 'Not Confirmed') |
| `action_date`  | datetime   |

#### Example Input:
| `text_id` | `email_id` | `signup_action` | `action_date`        |
|-----------|------------|-----------------|----------------------|
| 6878      | 125        | Confirmed       | 06/14/2022 00:00:00 |
| 6997      | 433        | Not Confirmed   | 07/09/2022 00:00:00 |
| 7000      | 433        | Confirmed       | 07/10/2022 00:00:00 |

#### Example Output:
| `user_id` |
|-----------|
| 1052      |

**Explanation:**
- User `1052` initially did not confirm their sign-up on the same day (`07/09/2022`), but confirmed on the second day (`07/10/2022`).
- User `7771` confirmed their sign-up on the first day, so they are not included in the output.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Join the Tables:** Use a `JOIN` to combine the `emails` table and `texts` table on the `email_id` column to get relevant data for each user.
2. **Filter for Confirmations:** Use the `WHERE` clause to filter rows where the `signup_action` is 'Confirmed'.
3. **Calculate Time Difference:** Subtract the `signup_date` from the `action_date` to find users who confirmed exactly one day after their sign-up date.
4. **Select Relevant Columns:** Output only the `user_id` for users meeting the above criteria.

---

### Query:
```sql
SELECT user_id 
FROM emails e 
JOIN texts t 
ON e.email_id = t.email_id
WHERE signup_action = 'Confirmed'
  AND EXTRACT(DAY FROM (action_date - signup_date)) = 1;
