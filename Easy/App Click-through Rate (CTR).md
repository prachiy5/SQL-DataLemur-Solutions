### Problem Description:
This is the same question as problem #1 in the SQL Chapter of Ace the Data Science Interview!

Assume you have an `events` table on Facebook app analytics. Write a query to calculate the click-through rate (CTR) for the app in 2022 and round the results to 2 decimal places.

#### Definition and Note:
- **Percentage of Click-Through Rate (CTR):**
  
  \[ CTR = 100.0 \times \frac{\text{Number of Clicks}}{\text{Number of Impressions}} \]

- To avoid integer division, multiply the CTR by 100.0, not 100.

---

#### `events` Table:
| Column Name  | Type       |
|--------------|------------|
| `app_id`     | integer    |
| `event_type` | string     |
| `timestamp`  | datetime   |

#### Example Input:
| `app_id` | `event_type` | `timestamp`           |
|----------|--------------|-----------------------|
| 123      | impression   | 07/18/2022 11:36:12  |
| 123      | impression   | 07/18/2022 11:37:12  |
| 123      | click        | 07/18/2022 11:37:42  |
| 234      | impression   | 07/18/2022 14:15:12  |
| 234      | click        | 07/18/2022 14:16:12  |

#### Example Output:
| `app_id` | `ctr`  |
|----------|--------|
| 123      | 50.00  |
| 234      | 100.00 |

**Explanation:**
- For App `123`, there are 2 impressions and 1 click. The CTR is calculated as:
  
  \[ CTR = 100.0 \times \frac{1}{2} = 50.00 \%
  \]

- For App `234`, there is 1 impression and 1 click. The CTR is calculated as:

  \[ CTR = 100.0 \times \frac{1}{1} = 100.00 \%
  \]

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Filter Data for 2022:** Use a `WHERE` clause to filter events that occurred in the year 2022.
2. **Calculate Clicks and Impressions:** Use conditional aggregation with `SUM` to count the number of `click` and `impression` events for each `app_id`.
3. **Compute CTR:** Divide the count of clicks by the count of impressions and multiply by 100.0 to calculate the CTR. Use the `ROUND` function to round the result to 2 decimal places.
4. **Output Results:** Display the `app_id` and the calculated CTR.

---

### Query:
```sql
WITH cte AS (
  SELECT *
  FROM events 
  WHERE EXTRACT(YEAR FROM timestamp) = 2022
),

cte2 AS (
  SELECT app_id, 
         ROUND(100.0 * SUM(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) /
                       SUM(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END), 2) AS ctr
  FROM cte
  GROUP BY app_id
)

SELECT *
FROM cte2;
