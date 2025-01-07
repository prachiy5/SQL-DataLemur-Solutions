### Problem Description:
Imagine you're an HR analyst at a tech company tasked with analyzing employee salaries. Your manager is keen on understanding the pay distribution and asks you to determine the second highest salary among all employees.

#### Key Points:
- It's possible that multiple employees may share the same second highest salary. In such cases, display the salary only once.

---

#### `employee` Schema:
| column_name    | type      | description                              |
|----------------|-----------|------------------------------------------|
| `employee_id`  | integer   | The unique ID of the employee.           |
| `name`         | string    | The name of the employee.                |
| `salary`       | integer   | The salary of the employee.              |
| `department_id`| integer   | The department ID of the employee.       |
| `manager_id`   | integer   | The manager ID of the employee.          |

#### Example Input:
| `employee_id` | `name`            | `salary` | `department_id` | `manager_id` |
|---------------|-------------------|----------|-----------------|--------------|
| 1             | Emma Thompson     | 3800     | 1               | 6            |
| 2             | Daniel Rodriguez  | 2230     | 1               | 7            |
| 3             | Olivia Smith      | 2000     | 1               | 8            |

#### Example Output:
| `second_highest_salary` |
|--------------------------|
| 2230                     |

**Explanation:**
- The highest salary is $3,800 (Emma Thompson).
- The second highest salary is $2,230 (Daniel Rodriguez).
- Even if multiple employees share the second highest salary, display it only once.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Find the Highest Salary:** Use the `MAX` function to identify the highest salary in the table.
2. **Filter Salaries Less Than the Highest:** Use a subquery to exclude the highest salary from consideration.
3. **Identify the Second Highest Salary:** Use the `MAX` function again on the filtered result to get the second highest salary.
4. **Display Unique Value:** Ensure only one value is returned, even if multiple employees share the second highest salary.

---

### Query:
```sql
SELECT MAX(salary) AS second_highest_salary
FROM employee 
WHERE salary < (
  SELECT MAX(salary) 
  FROM employee
);
