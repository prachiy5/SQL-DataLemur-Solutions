### Problem Description:
You’re a consultant for a major pizza chain that will be running a promotion where all 3-topping pizzas will be sold for a fixed price. To understand the costs involved, you need to calculate the total cost of all possible 3-topping combinations.

Given a list of pizza toppings and their costs:
1. Consider all possible combinations of 3 toppings.
2. Calculate the total cost of those 3 toppings.
3. Sort the results based on the highest total cost first.
4. Break ties by listing the ingredients in alphabetical order.

**Notes:**
- Ingredients in a combination must be unique (e.g., 'Pepperoni,Pepperoni,Onion' is invalid).
- Ingredients must be listed in alphabetical order (e.g., 'Chicken,Onions,Sausage').

---

### Input Table:
#### `pizza_toppings`
| Column Name    | Type          |
|----------------|---------------|
| `topping_name` | varchar(255)  |
| `ingredient_cost` | decimal(10,2) |

**Example Input:**
| `topping_name` | `ingredient_cost` |
|----------------|-------------------|
| Pepperoni      | 0.50              |
| Sausage        | 0.70              |
| Chicken        | 0.55              |
| Extra Cheese   | 0.40              |

---

### Desired Output:
| `pizza`                      | `total_cost` |
|------------------------------|--------------|
| Chicken,Pepperoni,Sausage    | 1.75         |
| Chicken,Extra Cheese,Sausage | 1.65         |
| Extra Cheese,Pepperoni,Sausage | 1.60         |
| Chicken,Extra Cheese,Pepperoni | 1.45         |

---

### Explanation:
- The pizza with toppings **Chicken, Pepperoni, and Sausage** has a cost of $0.55 + $0.50 + $0.70 = $1.75.
- The combinations are arranged alphabetically within each row, and the rows are sorted in descending order of `total_cost`.

---

### Thought Process:
1. **Generate Combinations:** Use a self-join to generate combinations of three toppings where each topping is unique, and ensure alphabetical order using inequality conditions (`<`).
2. **Calculate Costs:** Sum the `ingredient_cost` for each combination to calculate the total cost of the pizza.
3. **Format Results:** Concatenate the topping names in alphabetical order to display the pizza combination.
4. **Sort Results:** Sort the results by `total_cost` in descending order, and then by `pizza` name in ascending order to handle ties.

---

### SQL Query:
```sql
WITH combinations AS (
    SELECT 
        t1.topping_name AS topping1,
        t2.topping_name AS topping2,
        t3.topping_name AS topping3,
        t1.ingredient_cost + t2.ingredient_cost + t3.ingredient_cost AS total_cost
    FROM pizza_toppings t1
    INNER JOIN pizza_toppings t2 ON t1.topping_name < t2.topping_name
    INNER JOIN pizza_toppings t3 ON t2.topping_name < t3.topping_name
)
SELECT 
    CONCAT(topping1, ',', topping2, ',', topping3) AS pizza,
    ROUND(total_cost, 2) AS total_cost
FROM combinations
ORDER BY total_cost DESC, pizza ASC;
```

---

### Breakdown of the Query:
1. **`WITH combinations AS`:**
   - Create a temporary table `combinations` to generate all possible unique 3-topping combinations using self-joins.
   - Ensure alphabetical order using conditions `t1.topping_name < t2.topping_name` and `t2.topping_name < t3.topping_name`.
   - Calculate the total cost of each combination.

2. **`SELECT`:**
   - Concatenate the toppings in alphabetical order to form the `pizza` column.
   - Round the `total_cost` to 2 decimal places for better readability.

3. **`ORDER BY`:**
   - Sort the results primarily by `total_cost` in descending order.
   - For ties, sort by `pizza` in ascending alphabetical order.

---

### Output:
The query outputs the desired combinations of 3 toppings with their total costs, sorted as per the requirements.
