#### Problem Description 
Amazon wants to maximize the storage capacity of its 500,000 square-foot warehouse by prioritizing a specific batch of prime items. The specific prime product batch detailed in the inventory table must be maintained.

For example, if the prime product batch specified in the `item_category` column included 1 laptop and 1 side table, that would be the base batch. We could not add another laptop without also adding a side table; they come all together as a batch set.

After prioritizing the maximum number of prime batches, any remaining square footage will be utilized to stock non-prime batches, which also come in batch sets and cannot be separated into individual items.

Write a query to find the maximum number of prime and non-prime batches that can be stored in the 500,000 square feet warehouse based on the following criteria:
1. Prioritize stocking prime batches.
2. After accommodating prime items, allocate any remaining space to non-prime batches.

The output should include the `item_type` with `prime_eligible` listed first, followed by `not_prime`, along with the maximum number of batches that can be stocked.

Assumptions:
- Products must be stocked in batches, so we want to find the largest available quantity of prime batches, and then the largest available quantity of non-prime batches.
- Non-prime items must always be available in stock to meet customer demand, so the non-prime item count should never be zero.
- Item count should be whole numbers (integers).

--- Schema ---
**inventory** table:
| Column Name      | Type    | Description                                   |
|------------------|---------|-----------------------------------------------|
| `item_id`        | integer | The unique ID of the item.                   |
| `item_type`      | string  | Indicates if the item is `prime_eligible` or `not_prime`. |
| `item_category`  | string  | The category of the item (e.g., refrigerator, lamp). |
| `square_footage` | decimal | The square footage required for the item.    |

--- Example Input ---
| `item_id` | `item_type`      | `item_category`    | `square_footage` |
|-----------|------------------|--------------------|------------------|
| 1374      | prime_eligible   | mini refrigerator  | 68.00           |
| 4245      | not_prime        | standing lamp      | 26.40           |
| 2452      | prime_eligible   | television         | 85.00           |
| 3255      | not_prime        | side table         | 22.60           |
| 1672      | prime_eligible   | laptop             | 8.50            |

--- Example Output ---
| `item_type`      | `item_count` |
|------------------|--------------|
| prime_eligible   | 9285         |
| not_prime        | 6            |

Explanation:
- The largest number of prime batches (9285) is calculated based on the total square footage required for `prime_eligible` items.
- Remaining square footage is then used to calculate the largest possible number of non-prime batches (6).

--- Thought Process ---
1. **Summarize Data**: Group the data by `item_type` and calculate the total square footage required and the total number of items in each type.
2. **Calculate Prime Batches**: Divide the total warehouse space (500,000 square feet) by the total square footage for `prime_eligible` items to determine the maximum number of prime batches.
3. **Calculate Non-Prime Batches**: Deduct the square footage used by prime batches from the total warehouse space, then allocate the remaining space to `not_prime` items.
4. **Order Results**: Ensure the output is sorted with `prime_eligible` listed first, followed by `not_prime`.

--- Query ---
```sql
WITH summary AS (  
  SELECT  
    item_type,  
    SUM(square_footage) AS total_sqft,  
    COUNT(*) AS item_count  
  FROM inventory  
  GROUP BY item_type
),
prime_occupied_area AS (  
  SELECT  
    item_type,
    total_sqft,
    FLOOR(500000/total_sqft) AS prime_item_batch_count,
    (FLOOR(500000/total_sqft) * item_count) AS prime_item_count
  FROM summary  
  WHERE item_type = 'prime_eligible'
)

SELECT
  item_type,
  CASE 
    WHEN item_type = 'prime_eligible' 
      THEN (FLOOR(500000/total_sqft) * item_count)
    WHEN item_type = 'not_prime' 
      THEN FLOOR((500000 - (SELECT FLOOR(500000/total_sqft) * total_sqft FROM prime_occupied_area)) / total_sqft) * item_count
  END AS item_count
FROM summary
ORDER BY item_type DESC;
