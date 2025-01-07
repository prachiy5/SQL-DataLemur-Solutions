### Problem Description:
CVS Health is trying to better understand its pharmacy sales, and how well different products are selling. Each drug can only be produced by one manufacturer.

Write a query to find the top 3 most profitable drugs sold, and how much profit they made. Assume that there are no ties in the profits. Display the result from the highest to the lowest total profit.

#### Definition:
- **COGS** stands for Cost of Goods Sold, which is the direct cost associated with producing the drug.
- **Total Profit = Total Sales - Cost of Goods Sold**

---

#### `pharmacy_sales` Table:
| Column Name   | Type      |
|---------------|-----------|
| `product_id`  | integer   |
| `units_sold`  | integer   |
| `total_sales` | decimal   |
| `cogs`        | decimal   |
| `manufacturer`| varchar   |
| `drug`        | varchar   |

#### Example Input:
| `product_id` | `units_sold` | `total_sales` | `cogs`      | `manufacturer` | `drug`             |
|--------------|--------------|---------------|-------------|----------------|--------------------|
| 9            | 37410        | 293452.54     | 208876.01   | Eli Lilly      | Zyprexa            |
| 34           | 94698        | 600997.19     | 521182.16   | AstraZeneca    | Surmontil          |
| 61           | 77023        | 500101.61     | 419174.97   | Biogen         | Varicose Relief    |
| 136          | 144814       | 1084258       | 1006447.73  | Biogen         | Burkhart           |

#### Example Output:
| `drug`             | `total_profit` |
|--------------------|----------------|
| Zyprexa            | 84576.53       |
| Varicose Relief    | 80926.64       |
| Surmontil          | 79815.03       |

**Explanation:**
- Zyprexa made the most profit (of $84,576.53), followed by Varicose Relief (of $80,926.64), and Surmontil (of $79,815.03).
- The output lists the top 3 drugs based on their profitability in descending order.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Calculate Profit for Each Drug:** Use the formula `total_sales - cogs` to calculate the profit for each drug.
2. **Sort by Profit:** Order the results in descending order of the profit to prioritize the most profitable drugs.
3. **Limit to Top 3:** Use the `LIMIT` clause to fetch only the top 3 results.
4. **Select Relevant Columns:** Output the `drug` name and its calculated profit as `total_profit`.

---

### Query:
```sql
SELECT 
  drug, 
  total_sales - cogs AS profit 
FROM pharmacy_sales
ORDER BY profit DESC
LIMIT 3;
