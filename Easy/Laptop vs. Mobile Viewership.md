### Problem Description:
This is the same question as problem #3 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given the table on user viewership categorized by device type where the three types are **laptop**, **tablet**, and **phone**.

Write a query that calculates the total viewership for laptops and mobile devices, where **mobile** is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as `laptop_views` and the total viewership for mobile devices as `mobile_views`.

#### Effective 15 April 2023:
The solution has been updated with a more concise and easy-to-understand approach.

---

#### `viewership` Table:
| Column Name  | Type                              |
|--------------|-----------------------------------|
| `user_id`    | integer                           |
| `device_type`| string ('laptop', 'tablet', 'phone') |
| `view_time`  | timestamp                         |

#### Example Input:
| `user_id` | `device_type` | `view_time`          |
|-----------|---------------|----------------------|
| 123       | tablet        | 01/02/2022 00:00:00 |
| 125       | laptop        | 01/07/2022 00:00:00 |
| 128       | laptop        | 02/09/2022 00:00:00 |
| 129       | phone         | 02/09/2022 00:00:00 |
| 145       | tablet        | 02/24/2022 00:00:00 |

#### Example Output:
| `laptop_views` | `mobile_views` |
|----------------|----------------|
| 2              | 3              |

**Explanation:**
- Based on the example input, there are a total of 2 views from laptops and 3 views from mobile devices (tablet + phone).
- The query sums up the counts for each device category accordingly.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Define Mobile Devices:** Identify the rows where `device_type` is either `tablet` or `phone`.
2. **Separate Laptop Views:** Identify the rows where `device_type` is `laptop`.
3. **Use Conditional Aggregation:** Use `CASE` statements within the `SUM` function to calculate the total views for both categories.
4. **Output the Results:** Return the counts for `laptop_views` and `mobile_views` in the result.

---

### Query:
```sql
SELECT
  SUM(CASE WHEN device_type = 'tablet' OR device_type = 'phone' THEN 1 ELSE 0 END) AS mobile_views,
  SUM(CASE WHEN device_type = 'laptop' THEN 1 ELSE 0 END) AS laptop_views
FROM viewership;
