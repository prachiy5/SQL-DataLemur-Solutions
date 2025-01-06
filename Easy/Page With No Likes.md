### Problem Description:
Assume you're given two tables containing data about Facebook Pages and their respective likes (as in "Like a Facebook Page").

Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.

#### `pages` Table:
| Column Name | Type    |
|-------------|---------|
| `page_id`   | integer |
| `page_name` | varchar |

#### Example Input:
| `page_id` | `page_name`           |
|-----------|-----------------------|
| 20001     | SQL Solutions         |
| 20045     | Brain Exercises       |
| 20701     | Tips for Data Analysts|

#### `page_likes` Table:
| Column Name | Type       |
|-------------|------------|
| `user_id`   | integer    |
| `page_id`   | integer    |
| `liked_date`| datetime   |

#### Example Input:
| `user_id` | `page_id` | `liked_date`          |
|-----------|-----------|-----------------------|
| 111       | 20001     | 04/08/2022 00:00:00  |
| 121       | 20045     | 03/12/2022 00:00:00  |
| 156       | 20001     | 07/25/2022 00:00:00  |

#### Example Output:
| `page_id` |
|-----------|
| 20701     |

**Explanation:**
- The `pages` table contains a list of Facebook pages.
- The `page_likes` table contains information about which users liked which pages.
- Based on the data, Page ID `20701` is not present in the `page_likes` table, indicating it has zero likes. The query should identify such pages and return their IDs in ascending order.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Identify All Pages:** Use the `pages` table as the base to get all page IDs.
2. **Perform a LEFT JOIN:** Combine the `pages` table with the `page_likes` table using a LEFT JOIN to include all pages, even those without any matches in `page_likes`.
3. **Filter Pages with Zero Likes:** Use the `WHERE` clause to check for `NULL` values in the `page_id` column of the `page_likes` table, as this indicates no likes.
4. **Sort the Results:** Ensure the output is sorted by `page_id` in ascending order.

---

### Query:
```sql
SELECT p.page_id 
FROM pages p 
LEFT JOIN page_likes pl 
ON p.page_id = pl.page_id 
WHERE pl.page_id IS NULL 
ORDER BY p.page_id;
