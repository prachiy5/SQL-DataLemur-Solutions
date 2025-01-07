### Problem Description
Amazon Web Services (AWS) is powered by fleets of servers. Senior management has requested data-driven solutions to optimize server usage.

Write a query that calculates the total time that the fleet of servers was running. The output should be in units of full days.

#### Assumptions:

- Each server might start and stop several times.
- The total time in which the server fleet is running can be calculated as the sum of each server's uptime.

#### `server_utilization` Table:
| Column Name   | Type       |
|---------------|------------|
| server_id     | integer    |
| status_time   | timestamp  |
| session_status| string     |

#### Example Input:
| server_id | status_time          | session_status |
|-----------|----------------------|-----------------|
| 1         | 08/02/2022 10:00:00 | start           |
| 1         | 08/04/2022 10:00:00 | stop            |
| 2         | 08/17/2022 10:00:00 | start           |
| 2         | 08/24/2022 10:00:00 | stop            |

#### Example Output:
| total_uptime_days |
|--------------------|
| 21                |

### Explanation
- In the example output, the combined uptime of all the servers (from each start time to stop time) totals 21 full days.

The dataset you are querying against may have different input & output - this is just an example!

### Thought Process
1. Use a `WITH` clause to calculate start and stop times for each server by using the `LEAD` window function.
2. Identify rows where the `session_status` is `start` and there is a corresponding `stop` time.
3. Calculate the time difference between `stop_time` and `start_time` for each valid row.
4. Sum the total time and convert the result to full days using `DATE_PART` and `JUSTIFY_HOURS`.

### Query
```sql
WITH running_time AS (
  SELECT
    server_id,
    session_status,
    status_time AS start_time,
    LEAD(status_time) OVER (
      PARTITION BY server_id
      ORDER BY status_time
    ) AS stop_time
  FROM server_utilization
)

SELECT
  DATE_PART('days', JUSTIFY_HOURS(SUM(stop_time - start_time))) AS total_uptime_days
FROM running_time
WHERE session_status = 'start'
  AND stop_time IS NOT NULL;
