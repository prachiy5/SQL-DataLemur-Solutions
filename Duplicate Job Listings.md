### Problem Description:
This is the same question as problem #8 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given a table containing job postings from various companies on the LinkedIn platform. Write a query to retrieve the count of companies that have posted duplicate job listings.

#### Definition:
Duplicate job listings are defined as two job listings within the same company that share identical titles and descriptions.

---

#### `job_listings` Table:
| Column Name   | Type    |
|---------------|---------|
| `job_id`      | integer |
| `company_id`  | integer |
| `title`       | string  |
| `description` | string  |

#### Example Input:
| `job_id` | `company_id` | `title`           | `description`                                                                                     |
|----------|--------------|-------------------|---------------------------------------------------------------------------------------------------|
| 248      | 827          | Business Analyst  | Business analyst evaluates past and current business data with the primary goal of improving...   |
| 149      | 845          | Business Analyst  | Business analyst evaluates past and current business data with the primary goal of improving...   |
| 945      | 345          | Data Analyst      | Data analyst reviews data to identify key insights into a business's customers and ways...         |
| 164      | 345          | Data Analyst      | Data analyst reviews data to identify key insights into a business's customers and ways...         |
| 172      | 244          | Data Engineer     | Data engineer works in a variety of settings to build systems that collect, manage, and convert... |

#### Example Output:
| `duplicate_companies` |
|------------------------|
| 1                      |

**Explanation:**
- There is one company (`company_id` 345) that posted duplicate job listings.
- The duplicate listings, `job_id`s 945 and 164, have identical titles and descriptions.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Identify Duplicate Listings:** Use a `GROUP BY` clause on `company_id`, `title`, and `description` to group job listings that are identical within each company.
2. **Count Listings per Group:** Use `COUNT(job_id)` to find the number of listings in each group.
3. **Filter for Duplicates:** Use a `HAVING` clause to filter groups with more than one listing (`job_count > 1`).
4. **Count Companies with Duplicates:** Use `COUNT(DISTINCT company_id)` to count the unique companies that have duplicate listings.

---

### Query:
```sql
WITH cte AS (
  SELECT company_id, title, description, COUNT(job_id) AS job_count
  FROM job_listings
  GROUP BY company_id, title, description
)

SELECT COUNT(DISTINCT company_id) AS duplicate_companies
FROM cte
WHERE job_count > 1;
