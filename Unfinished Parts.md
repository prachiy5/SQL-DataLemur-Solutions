### Problem Description:
Tesla is investigating production bottlenecks and they need your help to extract the relevant data. Write a query to determine which parts have begun the assembly process but are not yet finished.

#### Assumptions:
1. The `parts_assembly` table contains all parts currently in production, each at varying stages of the assembly process.
2. An unfinished part is one that lacks a `finish_date`.
3. This question is straightforward, so let's approach it with simplicity in both thinking and solution.

#### Effective April 11th, 2023:
The problem statement and assumptions were updated to enhance clarity.

---

#### `parts_assembly` Table:
| Column Name    | Type       |
|----------------|------------|
| `part`         | string     |
| `finish_date`  | datetime   |
| `assembly_step`| integer    |

#### Example Input:
| `part`   | `finish_date`         | `assembly_step` |
|----------|-----------------------|-----------------|
| battery  | 01/22/2022 00:00:00  | 1               |
| battery  | 02/22/2022 00:00:00  | 2               |
| battery  | 03/22/2022 00:00:00  | 3               |
| bumper   | 01/22/2022 00:00:00  | 1               |
| bumper   | 02/22/2022 00:00:00  | 2               |
| bumper   | NULL                  | 3               |
| bumper   | NULL                  | 4               |

#### Example Output:
| `part`   | `assembly_step` |
|----------|-----------------|
| bumper   | 3               |
| bumper   | 4               |

**Explanation:**
- The `bumpers` in step 3 and 4 are the only items that remain unfinished as they lack a recorded `finish_date`.
- The query identifies these parts and their corresponding assembly steps.

The dataset you are querying against may have different input and output; this is just an example.

---

### Thought Process:
1. **Filter for Unfinished Parts:** Use the `WHERE` clause to filter rows where the `finish_date` is `NULL`.
2. **Select Relevant Columns:** Only select the `part` and `assembly_step` columns, as they are the required output.
3. **Display the Output:** Ensure the output shows the unfinished parts and their corresponding assembly steps.

---

### Query:
```sql
SELECT part, assembly_step
FROM parts_assembly
WHERE finish_date IS NULL;
