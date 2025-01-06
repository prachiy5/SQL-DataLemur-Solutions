### Problem Description:
This is the same question as problem #25 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given tables with information on Snapchat users, including their ages and time spent sending and opening snaps.

Write a query to obtain a breakdown of the time spent sending vs. opening snaps as a percentage of total time spent on these activities grouped by age group. Round the percentage to 2 decimal places in the output.

#### Notes:
- Calculate the following percentages:
  - **Time spent sending snaps:** \( \frac{\text{Time spent sending}}{\text{Time spent sending + Time spent opening}} \times 100 \)
  - **Time spent opening snaps:** \( \frac{\text{Time spent opening}}{\text{Time spent sending + Time spent opening}} \times 100 \)
- To avoid integer division in percentages, multiply by `100.0` and not `100`.
- Effective April 15th, 2023, the solution has been updated and optimized.

---

#### `activities` Table:
| Column Name     | Type       |
|-----------------|------------|
| `activity_id`   | integer    |
| `user_id`       | integer    |
| `activity_type` | string     | 
| `time_spent`    | float      |
| `activity_date` | datetime   |

#### Example Input:
| `activity_id` | `user_id` | `activity_type` | `time_spent` | `activity_date`        |
|---------------|-----------|-----------------|--------------|------------------------|
| 7274          | 123       | open            | 4.50         | 06/22/2022 12:00:00   |
| 2425          | 123       | send            | 3.50         | 06/22/2022 12:00:00   |
| 1413          | 456       | send            | 5.67         | 06/23/2022 12:00:00   |
| 1414          | 789       | chat            | 11.00        | 06/25/2022 12:00:00   |
| 2536          | 456       | open            | 3.00         | 06/25/2022 12:00:00   |

#### `age_breakdown` Table:
| Column Name     | Type       |
|-----------------|------------|
| `user_id`       | integer    |
| `age_bucket`    | string     | 

#### Example Input:
| `user_id` | `age_bucket` |
|-----------|--------------|
| 123       | 31-35        |
| 456       | 26-30        |
| 789       | 21-25        |

#### Example Output:
| `age_bucket` | `send_perc` | `open_perc` |
|--------------|-------------|-------------|
| 26-30        | 65.40       | 34.60       |
| 31-35        | 43.75       | 56.25       |

---

### Explanation:
Using the age bucket `26-30` as an example:
- Total time spent sending snaps: \( 5.67 \)
- Total time spent opening snaps: \( 3.00 \)
- Total time: \( 5.67 + 3.00 = 8.67 \)
- Percentage time spent sending snaps: \( \frac{5.67}{8.67} \times 100 = 65.40\% \)
- Percentage time spent opening snaps: \( \frac{3.00}{8.67} \times 100 = 34.60\% \)

---

### Thought Process:
1. **Join Tables:** Combine `activities` with `age_breakdown` to get the age bucket for each user.
2. **Aggregate Time Spent:** Use `SUM` and `CASE` to calculate the total time spent on `send` and `open` activities for each age bucket.
3. **Calculate Percentages:** Compute the percentages of time spent sending and opening snaps using the formula provided.
4. **Round and Format Results:** Use the `ROUND` function to format percentages to 2 decimal places.
5. **Sort Results:** Sort the output by age bucket in ascending order.

---

### Query:
```sql
WITH DATA AS (
  SELECT
    t1.user_id,
    age_bucket,
    SUM(CASE WHEN activity_type = 'open' THEN time_spent ELSE 0 END) AS open_time,
    SUM(CASE WHEN activity_type = 'send' THEN time_spent ELSE 0 END) AS send_time
  FROM activities t1
  LEFT JOIN age_breakdown t2 ON t1.user_id = t2.user_id
  GROUP BY 1, 2
)

SELECT
  age_bucket,
  ROUND(100.0 * (send_time / (send_time + open_time)), 2) AS send_perc,
  ROUND(100.0 * (open_time / (send_time + open_time)), 2) AS open_perc
FROM DATA
ORDER BY 1;
