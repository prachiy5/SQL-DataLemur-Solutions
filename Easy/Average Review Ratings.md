### Problem Description:
Given the `reviews` table, write a query to retrieve the average star rating for each product, grouped by month. The output should display the month as a numerical value, product ID, and average star rating rounded to two decimal places. Sort the output first by month and then by product ID.

---

#### `reviews` Table:
| Column Name   | Type               |
|---------------|--------------------|
| `review_id`   | integer            |
| `user_id`     | integer            |
| `submit_date` | datetime           |
| `product_id`  | integer            |
| `stars`       | integer (1-5)      |

#### Example Input:
| `review_id` | `user_id` | `submit_date`        | `product_id` | `stars` |
|-------------|-----------|----------------------|--------------|---------|
| 6171        | 123       | 06/08/2022 00:00:00 | 50001        | 4       |
| 7802        | 265       | 06/10/2022 00:00:00 | 69852        | 4       |
| 5293        | 362       | 06/18/2022 00:00:00 | 50001        | 3       |
| 6352        | 192       | 07/26/2022 00:00:00 | 69852        | 3       |
| 4517        | 981       | 07/05/2022 00:00:00 | 69852        | 2       |

#### Example Output:
| `mth` | `product` | `avg_stars` |
|-------|-----------|-------------|
| 6     | 50001     | 3.50        |
| 6     | 69852     | 4.00        |
| 7     | 69852     | 2.50        |

**Explanation:**
- Product 50001 received two ratings of 4 and 3 in the month of June (6th month), resulting in an average star rating of 3.5.
- The output shows the average star ratings rounded to two decimal places, grouped by month (`mth`) and product ID.
- The output is sorted first by month and then by product ID.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Extract Month:** Use the `EXTRACT(MONTH FROM submit_date)` function to get the month value from the `submit_date` column.
2. **Group Data:** Group the data by month and `product_id`.
3. **Calculate Average Rating:** Use the `AVG(stars)` function to calculate the average star rating for each group.
4. **Round the Average:** Use the `ROUND` function to round the average star rating to two decimal places.
5. **Sort Results:** Order the output by month (`mth`) and `product_id` in ascending order.

---

### Query:
```sql
SELECT EXTRACT(MONTH FROM submit_date) AS mth, 
       product_id, 
       ROUND(AVG(stars), 2) AS avg_stars
FROM reviews
GROUP BY 1, 2
ORDER BY 1, 2;
