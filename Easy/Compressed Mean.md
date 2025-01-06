### Problem Description:
You're trying to find the mean number of items per order on Alibaba, rounded to 1 decimal place, using tables that include information on the count of items in each order (`item_count`) and the corresponding number of orders for each item count (`order_occurrences`).

---

#### `items_per_order` Table:
| Column Name       | Type    |
|-------------------|---------|
| `item_count`      | integer |
| `order_occurrences` | integer |

#### Example Input:
| `item_count` | `order_occurrences` |
|--------------|---------------------|
| 1            | 500                 |
| 2            | 1000                |
| 3            | 800                 |
| 4            | 1000                |

---

#### Example Output:
| `mean` |
|--------|
| 2.7    |

---

**Explanation:**
- Total items = \((1 \times 500) + (2 \times 1000) + (3 \times 800) + (4 \times 1000) = 8900\)
- Total orders = \(500 + 1000 + 800 + 1000 = 3300\)
- Mean = \(\frac{8900}{3300} = 2.7\)

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Calculate Total Items:** Multiply each `item_count` by the corresponding `order_occurrences` and sum up the results.
2. **Calculate Total Orders:** Sum the `order_occurrences` column to get the total number of orders.
3. **Compute the Mean:** Divide the total items by the total orders and round the result to 1 decimal place.
4. **Output the Result:** Return the mean as a single value.

---

### Query:
```sql
SELECT 
  ROUND(SUM(item_count::decimal * order_occurrences) / SUM(order_occurrences), 1) AS mean
FROM items_per_order;
