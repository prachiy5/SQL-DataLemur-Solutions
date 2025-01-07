### Problem Description:
Companies often perform salary analyses to ensure fair compensation practices. One useful analysis is to check if there are any employees earning more than their direct managers.

As an HR Analyst, you're asked to identify all employees who earn more than their direct managers. The result should include the employee's ID and name.

---

#### `employee` Schema:
| column_name     | type      | description                               |
|-----------------|-----------|-------------------------------------------|
| `employee_id`   | integer   | The unique ID of the employee.            |
| `name`          | string    | The name of the employee.                 |
| `salary`        | integer   | The salary of the employee.               |
| `department_id` | integer   | The department ID of the employee.        |
| `manager_id`    | integer   | The manager ID of the employee.           |

#### Example Input:
| `employee_id` | `name`             | `salary` | `department_id` | `manager_id` |
|---------------|--------------------|----------|-----------------|--------------|
| 1             | Emma Thompson      | 3800     | 1               | 6            |
| 2             | Daniel Rodriguez   | 2230     | 1               | 7            |
| 3             | Olivia Smith       | 7000     | 1               | 8            |
| 4             | Noah Johnson       | 6800     | 2               | 9            |
| 5             | Sophia Martinez    | 1750     | 1               | 11           |
| 6             | Liam Brown         | 13000    | 3               | NULL         |
| 7             | Ava Garcia         | 12500    | 3               | NULL         |
| 8             | William Davis      | 6800     | 2               | NULL         |

#### Example Output:
| `employee_id` | `employee_name` |
|---------------|-----------------|
| 3             | Olivia Smith    |

**Explanation:**
- Olivia Smith earns $7,000, which is higher than her manager, William Davis, who earns $6,800.
- The output includes the `employee_id` and `name` of employees whose salary exceeds their manager's salary.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Self-Join the Table:** Use a self-join to combine the `employee` table with itself. The `manager_id` of an employee should match the `employee_id` of their manager.
2. **Compare Salaries:** Filter for rows where the employee's salary is greater than their manager's salary.
3. **Select Relevant Columns:** Retrieve the `employee_id` and `name` of employees meeting the condition.
4. **Output:** Display the results with the appropriate column names.

---

### Query:
```sql
SELECT 
  emp.employee_id AS employee_id,
  emp.name AS employee_name
FROM employee AS mgr
INNER JOIN employee AS emp
  ON mgr.employee_id = emp.manager_id
WHERE emp.salary > mgr.salary;


