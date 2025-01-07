
### Problem Description:
Google's marketing team is making a Superbowl commercial and needs a simple statistic to put on their TV ad: the median number of searches a person made last year.

However, at Google scale, querying the 2 trillion searches is too costly. Luckily, you have access to the `search_frequency` summary table which tells you the number of searches made last year and how many Google users fall into that bucket.

Write a query to report the median of searches made by a user. Round the median to one decimal point.

---
#### `search_frequency` Table:
| Column Name | Type     |
|-------------|----------|
| `searches`  | integer  |
| `num_users` | integer  |

---
#### Example Input:
| `searches` | `num_users` |
|------------|-------------|
| 1          | 2           |
| 2          | 2           |
| 3          | 3           |
| 4          | 1           |

---
#### Example Output:
| `median` |
|----------|
| 2.5      |

---
#### Explanation:
Expanding the `search_frequency` table gives us the following sequence:
[1, 1, 2, 2, 3, 3, 3, 4].

The median of this sequence is calculated as:
1. If the total number of users is odd, the median is the middle value.
2. If the total number of users is even, the median is the average of the two middle values.

In this example, the median of [1, 1, 2, 2, 3, 3, 3, 4] is (2 + 3) / 2 = 2.5.

---
### Thought Process:
1. **Understand the Problem**: We need the median of searches per user. Since the table summarizes the data, we need to expand it to calculate the median.
2. **Expand Data**: Use a function like `GENERATE_SERIES` to simulate rows for each user based on the `num_users` column.
3. **Calculate Median**:
   - Use the `percentile_cont(0.50)` function to calculate the 50th percentile (median) of the expanded `searches` data.
   - Round the result to one decimal place.

---
### Query:
```sql
WITH searches_expanded AS (
  SELECT searches
  FROM search_frequency
  GROUP BY 
    searches, 
    GENERATE_SERIES(1, num_users)
)

SELECT 
  ROUND(percentile_cont(0.50) WITHIN GROUP (ORDER BY searches)::DECIMAL, 1) AS median
FROM searches_expanded;
```

---
### Explanation of the Query:
1. **Expand Data**: The `WITH` clause creates a temporary table, `searches_expanded`, by repeating each `searches` value `num_users` times using `GENERATE_SERIES`.
2. **Calculate Median**:
   - The `percentile_cont(0.50)` function computes the 50th percentile (median) for the `searches` column.
   - The result is rounded to one decimal place using `ROUND`.
3. **Output the Result**: The query outputs the median as a single value.
