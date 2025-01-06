### Problem Description:
CVS Health is analyzing its pharmacy sales data, and how well different products are selling in the market. Each drug is exclusively manufactured by a single manufacturer.

Write a query to identify the manufacturers associated with the drugs that resulted in losses for CVS Health and calculate the total amount of losses incurred.

#### Requirements:
- Output the **manufacturer's name**.
- Count the **number of drugs** associated with losses.
- Calculate the **total losses in absolute value**.
- Display the results sorted in descending order with the highest losses displayed at the top.

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
| `product_id` | `units_sold` | `total_sales`  | `cogs`        | `manufacturer` | `drug`                      |
|--------------|--------------|----------------|---------------|----------------|-----------------------------|
| 156          | 89514        | 3130097.00     | 3427421.73    | Biogen         | Acyclovir                   |
| 25           | 222331       | 2753546.00     | 2974975.36    | AbbVie         | Lamivudine and Zidovudine   |
| 50           | 90484        | 2521023.73     | 2742445.90    | Eli Lilly      | Dermasorb TA Complete Kit   |
| 98           | 110746       | 813188.82      | 140422.87     | Biogen         | Medi-Chord                  |

#### Example Output:
| `manufacturer` | `drug_count` | `total_loss` |
|----------------|--------------|--------------|
| Biogen         | 1            | 297324.73    |
| AbbVie         | 1            | 221429.36    |
| Eli Lilly      | 1            | 221422.17    |

**Explanation:**
- Drugs with losses are identified where `total_sales - cogs <= 0`.
- Among the drugs:
  - **Biogen** has a drug (Acyclovir) with a loss of \(3427421.73 - 3130097.00 = 297324.73\).
  - **AbbVie** has a drug (Lamivudine and Zidovudine) with a loss of \(2974975.36 - 2753546.00 = 221429.36\).
  - **Eli Lilly** has a drug (Dermasorb TA Complete Kit) with a loss of \(2742445.90 - 2521023.73 = 221422.17\).
- The Medi-Chord drug by Biogen reported a profit and is excluded from the result.

---

### Thought Process:
1. **Identify Loss-Making Drugs:** Use the condition `total_sales - cogs <= 0` to filter out drugs with losses.
2. **Count Drugs and Calculate Losses per Manufacturer:** Group by `manufacturer` and calculate:
   - The count of drugs using `COUNT(drug)`.
   - The total loss using `SUM(total_sales - cogs)` and apply the absolute value.
3. **Sort Results:** Order the results by total loss in descending order to prioritize manufacturers with the highest losses.

---

### Query:
```sql
SELECT 
  manufacturer, 
  COUNT(drug) AS number_of_drugs_suffered_loss, 
  ABS(SUM(total_sales - cogs)) AS total_loss
FROM pharmacy_sales
WHERE total_sales - cogs <= 0
GROUP BY manufacturer
ORDER BY total_loss DESC;
