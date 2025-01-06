### Problem Description:
You're given a table containing the item count for each order on Alibaba, along with the frequency of orders that have the same item count. Write a query to retrieve the mode of the order occurrences. Additionally, if there are multiple item counts with the same mode, the results should be sorted in ascending order.

---

#### Clarifications:
- **`item_count`**: Represents the number of items sold in each order.
- **`order_occurrences`**: Represents the frequency of orders with the corresponding number of items sold per order.
- For example, if there are 800 orders with 3 items sold in each order, the record would have an `item_count` of 3 and an `order_occurrences` of 800.

---

#### Example Input:
| `item_count` | `order_occurrences` |
|--------------|----------------------|
| 1            | 500                  |
| 2            | 1000                 |
| 3            | 800                  |

---

#### Example Output:
| `mode` |
|--------|
| 2      |

---

#### Explanation:
- Based on the example output, the `order_occurrences` value of **1000** corresponds to the highest frequency among all `item_count` values.
- This means that `item_count` of **2** has occurred 1000 times, making it the mode of `order_occurrences`.

The dataset you are querying against may have different input & output; this is just an example.

---

### Thought Process:
1. **Find Maximum Occurrence:** Use the `MAX()` function to determine the highest value of `order_occurrences`.
2. **Filter Rows:** Select rows from the `items_per_order` table where `order_occurrences` equals the maximum value found in step 1.
3. **Sort Results (If Multiple Modes):** If there are multiple rows with the same `order_occurrences`, sort the results by `item_count` in ascending order.
4. **Output:** Display the `item_count` values corresponding to the mode.

---

### Query:
```sql
SELECT 
  item_count AS mode 
FROM 
  items_per_order 
WHERE 
  order_occurrences = (SELECT MAX(order_occurrences) FROM items_per_order);
