### Problem Description:
CVS Health wants to gain a clearer understanding of its pharmacy sales and the performance of various products.

Write a query to calculate the total drug sales for each manufacturer. Round the answer to the nearest million and report your results in descending order of total sales. In case of any duplicates, sort them alphabetically by the manufacturer name.

#### Additional Requirement:
- Since this data will be displayed on a dashboard viewed by business stakeholders, format your results as follows: `"$36 million"`.

---

#### `pharmacy_sales` Table:
| Column Name    | Type      |
|----------------|-----------|
| `product_id`   | integer   |
| `units_sold`   | integer   |
| `total_sales`  | decimal   |
| `cogs`         | decimal   |
| `manufacturer` | varchar   |
| `drug`         | varchar   |

#### Example Input:
| `product_id` | `units_sold` | `total_sales`  | `cogs`       | `manufacturer` | `drug`            |
|--------------|--------------|----------------|--------------|----------------|-------------------|
| 94           | 132362       | 2041758.41     | 1373721.70   | Biogen         | UP and UP         |
| 9            | 37410        | 293452.54      | 208876.01    | Eli Lilly      | Zyprexa           |
| 50           | 90484        | 2521023.73     | 2742445.90   | Eli Lilly      | Dermasorb         |
| 61           | 77023        | 500101.61      | 419174.97    | Biogen         | Varicose Relief   |
| 136          | 144814       | 1084258.00     | 1006447.73   | Biogen         | Burkhart          |

#### Example Output:
| `manufacturer` | `sale`      |
|----------------|-------------|
| Biogen         | $4 million  |
| Eli Lilly      | $3 million  |

**Explanation:**
- The total sales for Biogen is $4 million: 
  \((2041758.41 + 500101.61 + 1084258.00 = 3626118.02)\).
- The total sales for Eli Lilly is $3 million: 
  \((293452.54 + 2521023.73 = 2814476.27)\).
- Results are rounded to the nearest million and formatted as required.

---

### Thought Process:
1. **Aggregate Total Sales:** Use `SUM(total_sales)` to calculate the total sales for each manufacturer.
2. **Format Results:** Divide the total sales by 1,000,000 and round to the nearest integer using `ROUND`. Use `CONCAT` to format the results as `"$X million"`.
3. **Sort Results:** Order the results by total sales in descending order. For ties, sort alphabetically by `manufacturer`.
4. **Group by Manufacturer:** Ensure results are grouped by each unique `manufacturer`.

---

### Query:
```sql
SELECT 
  manufacturer, 
  CONCAT('$', ROUND(SUM(total_sales) / 1000000), ' million') AS sale 
FROM pharmacy_sales
GROUP BY manufacturer 
ORDER BY SUM(total_sales) DESC, manufacturer;
