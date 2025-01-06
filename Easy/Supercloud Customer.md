### Problem Description:
A Microsoft Azure Supercloud customer is defined as a customer who has purchased at least one product from every product category listed in the `products` table.

Write a query that identifies the customer IDs of these Supercloud customers.

---

#### `customer_contracts` Table:
| Column Name   | Type    |
|---------------|---------|
| `customer_id` | integer |
| `product_id`  | integer |
| `amount`      | integer |

#### Example Input:
| `customer_id` | `product_id` | `amount` |
|---------------|-------------|----------|
| 1             | 1           | 1000     |
| 1             | 3           | 2000     |
| 1             | 5           | 1500     |
| 2             | 2           | 3000     |
| 2             | 6           | 2000     |

---

#### `products` Table:
| Column Name       | Type    |
|-------------------|---------|
| `product_id`      | integer |
| `product_category`| string  |
| `product_name`    | string  |

#### Example Input:
| `product_id` | `product_category` | `product_name`            |
|--------------|--------------------|---------------------------|
| 1            | Analytics          | Azure Databricks          |
| 2            | Analytics          | Azure Stream Analytics    |
| 4            | Containers         | Azure Kubernetes Service  |
| 5            | Containers         | Azure Service Fabric      |
| 6            | Compute            | Virtual Machines          |
| 7            | Compute            | Azure Functions           |

---

#### Example Output:
| `customer_id` |
|---------------|
| 1             |

**Explanation:**
- Customer 1 bought products from all categories: `Analytics`, `Containers`, and `Compute`, making them a Supercloud customer.
- Customer 2 is not a Supercloud customer since they did not purchase any product in the `Containers` category.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Identify Unique Categories:** Use the `products` table to determine all distinct product categories.
2. **Count Categories per Customer:** Join the `customer_contracts` table with the `products` table to associate purchases with categories. Then group by `customer_id` and count the number of distinct categories each customer has purchased from.
3. **Compare with Total Categories:** Filter customers who have purchased from all categories by comparing their category count to the total number of unique categories in the `products` table.
4. **Order Results:** Return the `customer_id` values in ascending order.

---

### Query:
```sql
WITH supercloud AS (
  SELECT 
    customers.customer_id, 
    COUNT(DISTINCT products.product_category) AS unique_count
  FROM customer_contracts AS customers
  LEFT JOIN products 
    ON customers.product_id = products.product_id
  GROUP BY customers.customer_id
)

SELECT customer_id
FROM supercloud
WHERE unique_count = (
  SELECT COUNT(DISTINCT product_category) FROM products
)
ORDER BY customer_id;
