### Problem Description:
Your team at JPMorgan Chase is preparing to launch a new credit card, and to gain some insights, you're analyzing how many credit cards were issued each month.

Write a query that outputs the name of each credit card and the difference in the number of issued cards between the month with the highest issuance cards and the lowest issuance. Arrange the results based on the largest disparity.

---

#### `monthly_cards_issued` Table:
| Column Name    | Type    |
|----------------|---------|
| `card_name`    | string  |
| `issued_amount`| integer |
| `issue_month`  | integer |
| `issue_year`   | integer |

#### Example Input:
| `card_name`            | `issued_amount` | `issue_month` | `issue_year` |
|------------------------|-----------------|---------------|--------------|
| Chase Freedom Flex     | 55000          | 1             | 2021         |
| Chase Freedom Flex     | 60000          | 2             | 2021         |
| Chase Freedom Flex     | 65000          | 3             | 2021         |
| Chase Freedom Flex     | 70000          | 4             | 2021         |
| Chase Sapphire Reserve | 170000         | 1             | 2021         |
| Chase Sapphire Reserve | 175000         | 2             | 2021         |
| Chase Sapphire Reserve | 180000         | 3             | 2021         |

#### Example Output:
| `card_name`            | `difference` |
|------------------------|--------------|
| Chase Freedom Flex     | 15000        |
| Chase Sapphire Reserve | 10000        |

**Explanation:**
- **Chase Freedom Flex:**
  - Best month: 70,000 cards issued
  - Worst month: 55,000 cards issued
  - Difference: 70,000 - 55,000 = 15,000

- **Chase Sapphire Reserve:**
  - Best month: 180,000 cards issued
  - Worst month: 170,000 cards issued
  - Difference: 180,000 - 170,000 = 10,000

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Identify Highest and Lowest Issuance Months:** Use the `MAX` and `MIN` aggregate functions to find the highest and lowest number of cards issued for each credit card.
2. **Calculate the Difference:** Subtract the minimum issuance from the maximum issuance to calculate the disparity.
3. **Group by Credit Card Name:** Use the `GROUP BY` clause to ensure calculations are performed for each `card_name`.
4. **Sort Results:** Arrange the output in descending order of the difference to prioritize cards with the largest disparities.

---

### Query:
```sql
SELECT 
  card_name, 
  MAX(issued_amount) - MIN(issued_amount) AS difference
FROM monthly_cards_issued
GROUP BY card_name
ORDER BY difference DESC;
