### Problem Description:
You are given a table containing Twitter tweet data. Write a query to obtain a histogram of tweets posted per user in 2022. The output should show the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

#### `tweets` Table:
| Column Name  | Type       |
|--------------|------------|
| `tweet_id`   | integer    |
| `user_id`    | integer    |
| `msg`        | string     |
| `tweet_date` | timestamp  |

#### Example Input:
| `tweet_id` | `user_id` | `msg`                                                              | `tweet_date`          |
|------------|-----------|-------------------------------------------------------------------|-----------------------|
| 214252     | 111       | Am considering taking Tesla private at $420. Funding secured.    | 12/30/2021 00:00:00   |
| 739252     | 111       | Despite the constant negative press covfefe                     | 01/01/2022 00:00:00   |
| 846402     | 111       | Following @NickSinghTech on Twitter changed my life!             | 02/14/2022 00:00:00   |
| 241425     | 254       | If the salary is so competitive why won’t you tell me what it is? | 03/01/2022 00:00:00   |
| 231574     | 148       | I no longer have a manager. I can't be managed                   | 03/23/2022 00:00:00   |

#### Example Output:
| `tweet_bucket` | `users_num` |
|----------------|-------------|
| 1              | 2           |
| 2              | 1           |

**Explanation:**
- Based on the example output, there are two users who posted only one tweet in 2022.
- One user posted two tweets in 2022.
- The query groups the users by the number of tweets they posted and displays the number of users in each group.

The dataset you are querying against may have different input and output; this is just an example.

### Thought Process:
1. **Filter the Data:** Identify tweets from the year 2022 using the `EXTRACT(YEAR FROM tweet_date)` function.
2. **Count Tweets per User:** For each `user_id`, count the number of tweets they posted in 2022. This forms the `tweet_bucket`.
3. **Group by Buckets:** Count how many users fall into each `tweet_bucket`.
4. **Display Results:** Return the `tweet_bucket` (number of tweets per user) and `users_num` (number of users in that bucket).

### Query:
```sql
WITH cte AS (
  SELECT user_id, COUNT(tweet_id) AS tweet_buckets
  FROM tweets
  WHERE EXTRACT(YEAR FROM tweet_date) = '2022'
  GROUP BY user_id
)

SELECT tweet_buckets, COUNT(user_id) AS users_num
FROM cte
GROUP BY tweet_buckets;
