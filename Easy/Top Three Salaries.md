### Problem Description:
As part of an ongoing analysis of salary distribution within the company, your manager has requested a report identifying high earners in each department. A 'high earner' within a department is defined as an employee with a salary ranking among the top three salaries within that department.

You're tasked with identifying these high earners across all departments. Write a query to display the employee's name along with their department name and salary. In case of duplicates, sort the results of department name in ascending order, then by salary in descending order. If multiple employees have the same salary, then order them alphabetically.

#### Notes:
- Ensure to utilize the appropriate ranking window function to handle duplicate salaries effectively.
- As of June 18th, we have removed the requirement for unique salaries and revised the sorting order for the results.

---

#### `employee` Schema:
| column_name    | type      | description                             |
|----------------|-----------|-----------------------------------------|
| `employee_id`  | integer   | The unique ID of the employee.          |
| `name`         | string    | The name of the employee.               |
| `salary`       | integer   | The salary of the employee.             |
| `department_id`| integer   | The department ID of the employee.      |
| `manager_id`   | integer   | The manager ID of the employee.         |

#### `department` Schema:
| column_name     | type      | description                             |
|-----------------|-----------|-----------------------------------------|
| `department_id` | integer   | The department ID of the employee.      |
| `department_name`| string   | The name of the department.             |

#### Example Input:
**`employee` Table:**
| `employee_id` | `name`            | `salary` | `department_id` | `manager_id` |
|---------------|-------------------|----------|-----------------|--------------|
| 1             | Emma Thompson     | 3800     | 1               | 6            |
| 2             | Daniel Rodriguez  | 2230     | 1               | 7            |
| 3             | Olivia Smith      | 2000     | 1               | 8            |
| 4             | Noah Johnson      | 6800     | 2               | 9            |
| 5             | Sophia Martinez   | 1750     | 1               | 11           |
| 6             | Liam Brown        | 13000    | 3               | NULL         |
| 7             | Ava Garcia        | 12500    | 3               | NULL         |
| 8             | William Davis     | 6800     | 2               | NULL         |
| 9             | Isabella Wilson   | 11000    | 3               | NULL         |
| 10            | James Anderson    | 4000     | 1               | 11           |

**`department` Table:**
| `department_id` | `department_name` |
|-----------------|-------------------|
| 1               | Data Analytics    |
| 2               | Data Science      |

#### Example Output:
| `department_name` | `name`           | `salary` |
|-------------------|------------------|----------|
| Data Analytics    | James Anderson   | 4000     |
| Data Analytics    | Emma Thompson    | 3800     |
| Data Analytics    | Daniel Rodriguez | 2230     |
| Data Science      | Noah Johnson     | 6800     |
| Data Science      | William Davis    | 6800     |

**Explanation:**
- In the **Data Analytics** department:
  - James Anderson leads with a salary of $4,000.
  - Emma Thompson follows with $3,800.
  - Daniel Rodriguez ranks third with $2,230.
- In the **Data Science** department:
  - Noah Johnson and William Davis earn $6,800. Noah is listed first due to alphabetical ordering.

---

### Thought Process:
1. **Rank Employees Within Departments:** Use the `DENSE_RANK()` function to rank employees by their salary within each department in descending order.
2. **Filter Top 3 Salaries Per Department:** Include only employees with a rank of 3 or less.
3. **Join with Department Data:** Use an `INNER JOIN` to include department names in the final output.
4. **Sort Results:** Order the output by `department_name` (ascending), `salary` (descending), and `name` (alphabetically for ties).

---

### Query:
```sql
WITH ranked_salary AS (
  SELECT 
    name,
    salary,
    department_id,
    DENSE_RANK() OVER (
      PARTITION BY department_id ORDER BY salary DESC) AS ranking
  FROM employee
)

SELECT 
  d.department_name,
  rs.name,
  rs.salary
FROM ranked_salary AS rs
INNER JOIN department AS d
  ON rs.department_id = d.department_id
WHERE rs.ranking <= 3
ORDER BY d.department_name, rs.salary DESC, rs.name ASC;
