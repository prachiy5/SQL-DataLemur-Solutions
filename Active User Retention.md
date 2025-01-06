--- Problem Description ---
Assume you're given a table containing information on Facebook user actions. Write a query to obtain the number of monthly active users (MAUs) in July 2022, including the month in numerical format "1, 2, 3".

Hint:
An active user is defined as a user who has performed actions such as 'sign-in', 'like', or 'comment' in both the current month and the previous month.

--- Schema: user_actions Table ---
| Column Name | Type       | Description                                  |
|-------------|------------|----------------------------------------------|
| user_id     | integer    | Unique identifier for a Facebook user.      |
| event_id    | integer    | Unique identifier for the user event.       |
| event_type  | string     | Type of action ('sign-in', 'like', 'comment').|
| event_date  | datetime   | Date and time of the user action.           |

--- Example Input ---
| user_id | event_id | event_type | event_date            |
|---------|----------|------------|-----------------------|
| 445     | 7765     | sign-in    | 05/31/2022 12:00:00  |
| 742     | 6458     | sign-in    | 06/03/2022 12:00:00  |
| 445     | 3634     | like       | 06/05/2022 12:00:00  |
| 742     | 1374     | comment    | 06/05/2022 12:00:00  |
| 648     | 3124     | like       | 06/18/2022 12:00:00  |

--- Example Output for June 2022 ---
| month | monthly_active_users |
|-------|-----------------------|
| 6     | 1                     |

Explanation:
In June 2022, there was only one monthly active user (MAU) with the user_id 445. This user performed actions in both the current month and the previous month.

--- Query ---
```sql
WITH cte AS (
    SELECT 
        user_id, 
        EXTRACT(MONTH FROM event_date) AS month,
        EXTRACT(YEAR FROM event_date) AS year
    FROM user_actions
    WHERE EXTRACT(YEAR FROM event_date) = 2022
)
SELECT 
    c1.month AS month,
    COUNT(DISTINCT c1.user_id) AS monthly_active_users
FROM cte c1
JOIN cte c2
  ON c1.user_id = c2.user_id
  AND c1.year = c2.year
  AND c1.month = 7 -- Current month (July)
  AND c2.month = 6 -- Previous month (June)
GROUP BY c1.month;
```

--- Thought Process ---
1. Extract the `month` and `year` from the `event_date` column to work with temporal data effectively.
2. Use a Common Table Expression (CTE) to filter data for the year 2022 and prepare it for analysis.
3. Perform a self-join on the CTE to find users who performed actions in both the current month (July) and the previous month (June).
4. Group by the `month` column to calculate the number of distinct users meeting the criteria.
5. Output the `month` and the count of `monthly_active_users`.

--- Key Points ---
- The self-join is essential to compare user activity across consecutive months.
- The `COUNT(DISTINCT user_id)` ensures that each user is counted only once.
- The results are filtered for July 2022, as specified in the problem statement.
- The output is sorted by the month to ensure clarity in results.
