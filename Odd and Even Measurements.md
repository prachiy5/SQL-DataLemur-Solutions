### Problem Description:
This is the same question as problem #28 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given a table with measurement values obtained from a Google sensor over multiple days with measurements taken multiple times within each day.

Write a query to calculate the sum of odd-numbered and even-numbered measurements separately for a particular day and display the results in two different columns. Refer to the Example Output below for the desired format.

---

#### Definition:
- Within a day, measurements taken at 1st, 3rd, and 5th times are considered odd-numbered measurements.
- Measurements taken at 2nd, 4th, and 6th times are considered even-numbered measurements.

#### Assumption:
- Effective April 15th, 2023, the question and solution for this question have been revised.

---

#### `measurements` Table:
| Column Name        | Type      |
|--------------------|-----------|
| `measurement_id`   | integer   |
| `measurement_value`| decimal   |
| `measurement_time` | datetime  |

#### Example Input:
| `measurement_id` | `measurement_value` | `measurement_time`       |
|------------------|---------------------|--------------------------|
| 131233           | 1109.51            | 07/10/2022 09:00:00      |
| 135211           | 1662.74            | 07/10/2022 11:00:00      |
| 523542           | 1246.24            | 07/10/2022 13:15:00      |
| 143562           | 1124.50            | 07/11/2022 15:00:00      |
| 346462           | 1234.14            | 07/11/2022 16:45:00      |

---

#### Example Output:
| `measurement_day`   | `odd_sum` | `even_sum` |
|---------------------|-----------|------------|
| 07/10/2022 00:00:00 | 2355.75   | 1662.74    |
| 07/11/2022 00:00:00 | 1124.50   | 1234.14    |

---

#### Explanation:
- On **07/10/2022**, the sum of the odd-numbered measurements is \(1109.51 + 1246.24 = 2355.75\), while the sum of the even-numbered measurements is \(1662.74\).
- On **07/11/2022**, there are only two measurements available. The sum of the odd-numbered measurements is \(1124.50\), and the sum of the even-numbered measurements is \(1234.14\).

The dataset you are querying against may have different input & output - this is just an example.

---

### Thought Process:
1. **Rank Measurements:** Assign a sequential number to measurements for each day using the `ROW_NUMBER()` function, ordered by `measurement_time`.
2. **Separate Odd and Even Measurements:** Use the sequential number to determine whether a measurement is odd or even using modulo arithmetic.
3. **Aggregate by Day:** Group measurements by their respective day and compute separate sums for odd and even measurements.
4. **Output Format:** Ensure the output displays the day, sum of odd measurements, and sum of even measurements.

---

### Query:
```sql
WITH ranked_measurements AS (
  SELECT 
    CAST(measurement_time AS DATE) AS measurement_day,
    measurement_value,
    ROW_NUMBER() OVER (
      PARTITION BY CAST(measurement_time AS DATE)
      ORDER BY measurement_time
    ) AS measurement_num
  FROM measurements
)

SELECT 
  measurement_day,
  SUM(measurement_value) FILTER (WHERE measurement_num % 2 != 0) AS odd_sum,
  SUM(measurement_value) FILTER (WHERE measurement_num % 2 = 0) AS even_sum
FROM ranked_measurements
GROUP BY measurement_day;
