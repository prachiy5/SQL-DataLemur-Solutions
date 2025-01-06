### Problem Description:
This is the same question as problem #2 in the SQL Chapter of Ace the Data Science Interview!

Assume you're given the tables containing completed trade orders and user details in a Robinhood trading system.

Write a query to retrieve the top three cities that have the highest number of completed trade orders listed in descending order. Output the city name and the corresponding number of completed trade orders.

---

#### `trades` Table:
| Column Name   | Type                          |
|---------------|-------------------------------|
| `order_id`    | integer                       |
| `user_id`     | integer                       |
| `quantity`    | integer                       |
| `status`      | string ('Completed', 'Cancelled') |
| `date`        | timestamp                     |
| `price`       | decimal (5, 2)               |

#### Example Input:
| `order_id` | `user_id` | `quantity` | `status`    | `date`              | `price` |
|------------|-----------|------------|-------------|---------------------|---------|
| 100101     | 111       | 10         | Cancelled   | 08/17/2022 12:00:00 | 9.80    |
| 100102     | 111       | 10         | Completed   | 08/17/2022 12:00:00 | 10.00   |
| 100259     | 148       | 35         | Completed   | 08/25/2022 12:00:00 | 5.10    |
| 100264     | 148       | 40         | Completed   | 08/26/2022 12:00:00 | 4.80    |
| 100305     | 300       | 15         | Completed   | 09/05/2022 12:00:00 | 10.00   |
| 100400     | 178       | 32         | Completed   | 09/17/2022 12:00:00 | 12.00   |
| 100565     | 265       | 2          | Completed   | 09/27/2022 12:00:00 | 8.70    |

#### `users` Table:
| Column Name   | Type       |
|---------------|------------|
| `user_id`     | integer    |
| `city`        | string     |
| `email`       | string     |
| `signup_date` | datetime   |

#### Example Input:
| `user_id` | `city`           | `email`                        | `signup_date`       |
|-----------|------------------|--------------------------------|---------------------|
| 111       | San Francisco    | rrok10@gmail.com               | 08/03/2021 12:00:00 |
| 148       | Boston           | sailor9820@gmail.com           | 08/20/2021 12:00:00 |
| 178       | San Francisco    | harrypotterfan182@gmail.com    | 01/05/2022 12:00:00 |
| 265       | Denver           | shadower_@hotmail.com          | 02/26/2022 12:00:00 |
| 300       | San Francisco    | houstoncowboy1122@hotmail.com  | 06/30/2022 12:00:00 |

#### Example Output:
| `city`          | `total_orders` |
|------------------|----------------|
| San Francisco    | 3              |
| Boston           | 2              |
| Denver           | 1              |

**Explanation:**
- In the given dataset, San Francisco has the highest number of completed trade orders with 3 orders.
- Boston holds the second position with 2 orders.
- Denver ranks third with 1 order.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Filter Completed Trades:** Use the `WHERE` clause to select only the rows where `status` is 'Completed' in the `trades` table.
2. **Join Tables:** Use an `INNER JOIN` to combine the `trades` table with the `users` table on the `user_id` column to get the city for each trade.
3. **Count Orders per City:** Use `GROUP BY` on the `city` column and count the number of completed orders for each city.
4. **Sort Results:** Use `ORDER BY` to sort the cities in descending order of the total number of completed orders.
5. **Limit Output:** Use `LIMIT` to retrieve only the top three cities.

---

### Query:
```sql
SELECT city, COUNT(order_id) AS total_orders
FROM trades t
INNER JOIN users u
ON t.user_id = u.user_id
WHERE status = 'Completed'
GROUP BY city
ORDER BY total_orders DESC
LIMIT 3;
