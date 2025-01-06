### Problem Description:
Write a query to identify the top 2 Power Users who sent the highest number of messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending order based on the count of the messages.

#### Assumption:
- No two users have sent the same number of messages in August 2022.

---

#### `messages` Table:
| Column Name   | Type       |
|---------------|------------|
| `message_id`  | integer    |
| `sender_id`   | integer    |
| `receiver_id` | integer    |
| `content`     | varchar    |
| `sent_date`   | datetime   |

#### Example Input:
| `message_id` | `sender_id` | `receiver_id` | `content`               | `sent_date`           |
|--------------|-------------|---------------|-------------------------|-----------------------|
| 901          | 3601        | 4500          | You up?                | 08/03/2022 00:00:00  |
| 902          | 4500        | 3601          | Only if you're buying  | 08/03/2022 00:00:00  |
| 743          | 3601        | 8752          | Let's take this offline| 06/14/2022 00:00:00  |
| 922          | 3601        | 4500          | Get on the call         | 08/10/2022 00:00:00  |

#### Example Output:
| `sender_id` | `message_count` |
|-------------|-----------------|
| 3601        | 2               |
| 4500        | 1               |

**Explanation:**
- `sender_id` 3601 sent 2 messages in August 2022.
- `sender_id` 4500 sent 1 message in August 2022.
- The output shows the top 2 users with the highest message counts in descending order.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Filter Messages for August 2022:** Use the `WHERE` clause to include only messages sent between `2022-08-01` and `2022-08-31`.
2. **Count Messages by Sender:** Group messages by `sender_id` and count the number of messages for each sender.
3. **Sort Results:** Order the results by message count in descending order to find the top senders.
4. **Limit the Output:** Use the `LIMIT` clause to return only the top 2 users.

---

### Query:
```sql
SELECT sender_id, COUNT(message_id) AS message_count
FROM messages
WHERE sent_date >= '2022-08-01' AND sent_date < '2022-09-01'
GROUP BY sender_id
ORDER BY message_count DESC
LIMIT 2;
