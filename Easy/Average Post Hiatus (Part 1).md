### Problem Description:
Given a table of Facebook posts, for each user who posted at least twice in 2021, write a query to find the number of days between each user’s first post of the year and last post of the year in the year 2021. Output the user and the number of days between each user's first and last post.

#### `posts` Table:
| Column Name     | Type       |
|-----------------|------------|
| `user_id`       | integer    |
| `post_id`       | integer    |
| `post_content`  | text       |
| `post_date`     | timestamp  |

#### Example Input:
| `user_id` | `post_id` | `post_content`                                              | `post_date`           |
|-----------|-----------|------------------------------------------------------------|-----------------------|
| 151652    | 599415    | Need a hug                                                  | 07/10/2021 12:00:00  |
| 661093    | 624356    | Bed. Class 8-12. Work 12-3. Gym 3-5 or 6. Then class 6-10. | 07/29/2021 13:00:00  |
| 004239    | 784254    | Happy 4th of July!                                          | 07/04/2021 11:00:00  |
| 661093    | 442560    | Just going to cry myself to sleep after watching Marley...  | 07/08/2021 14:00:00  |
| 151652    | 111766    | I'm so done with covid - need travelling ASAP!              | 07/12/2021 19:00:00  |

#### Example Output:
| `user_id` | `days_between` |
|-----------|----------------|
| 151652    | 2              |
| 661093    | 21             |

**Explanation:**
- `user_id` 151652 has posts on **07/10/2021** and **07/12/2021**, so the difference between the first and last post is 2 days.
- `user_id` 661093 has posts on **07/08/2021** and **07/29/2021**, so the difference between the first and last post is 21 days.
- Only users with at least two posts in 2021 are included in the output.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Filter Posts for 2021:** Use the `WHERE` clause to filter rows where the `post_date` is in the year 2021.
2. **Find First and Last Posts:** Use `MIN(post_date)` and `MAX(post_date)` for each `user_id` to find the first and last post dates.
3. **Calculate the Difference:** Subtract the earliest post date from the latest post date to calculate the number of days.
4. **Filter Users with At Least Two Posts:** Use the `HAVING` clause to include only users who have posted at least twice.

---

### Query:
```sql
SELECT user_id,
       MAX(post_date::DATE) - MIN(post_date::DATE) AS days_between
FROM posts
WHERE DATE_PART('year', post_date) = 2021
GROUP BY user_id
HAVING COUNT(*) >= 2;
