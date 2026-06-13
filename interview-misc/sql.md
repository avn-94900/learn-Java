# SQL: Find the 2nd Highest Salary

## Sample Data

### employee

| name | salary |
| ---- | ------ |
| A    | 10000  |
| B    | 10000  |
| C    | 9000   |
| D    | 9000   |
| E    | 8000   |

---

# Method 1: ORDER BY + LIMIT + OFFSET

```sql id="t9ldk4"
SELECT salary
FROM employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

### Sorted Data

| salary |
| ------ |
| 10000  |
| 10000  |
| 9000   |
| 9000   |
| 8000   |

### Result

| salary |
| ------ |
| 10000  |

### Problem

This returns the **second row**, not the **second highest distinct salary**.

Duplicates cause incorrect results.

---

# Method 2: DISTINCT + LIMIT + OFFSET

```sql id="l3b9m4"
SELECT DISTINCT salary
FROM employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

### After DISTINCT

| salary |
| ------ |
| 10000  |
| 9000   |
| 8000   |

### Result

| salary |
| ------ |
| 9000   |

### Advantage

Removes duplicate salaries before applying OFFSET.

---

# Method 3: DENSE_RANK() Using Subquery

```sql id="6hxb75"
SELECT name, salary
FROM (
    SELECT name,
           salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employee
) t
WHERE rnk = 2;
```

### Inner Query Output

| name | salary | rnk |
| ---- | ------ | --- |
| A    | 10000  | 1   |
| B    | 10000  | 1   |
| C    | 9000   | 2   |
| D    | 9000   | 2   |
| E    | 8000   | 3   |

### Result

| name | salary |
| ---- | ------ |
| C    | 9000   |
| D    | 9000   |

### Advantage

Returns all employees having the second-highest distinct salary.

---

# Method 4: DENSE_RANK() Using CTE (WITH)

```sql id="3wkvrh"
WITH t AS (
    SELECT name,
           salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employee
)
SELECT name,
       salary
FROM t
WHERE rnk = 2;
```

### CTE Output (`t`)

| name | salary | rnk |
| ---- | ------ | --- |
| A    | 10000  | 1   |
| B    | 10000  | 1   |
| C    | 9000   | 2   |
| D    | 9000   | 2   |
| E    | 8000   | 3   |

### Final Result

| name | salary |
| ---- | ------ |
| C    | 9000   |
| D    | 9000   |

---

# Common Mistake

❌ Wrong

```sql id="s4h6ra"
SELECT salary, name
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employee
) t
WHERE rnk = 2;
```

### Why?

The inner query returns only:

| salary | rnk |
| ------ | --- |
| 10000  | 1   |
| 10000  | 1   |
| 9000   | 2   |
| 9000   | 2   |
| 8000   | 3   |

There is **no `name` column** available to the outer query.

---

# RANK() vs DENSE_RANK()

### Data

| salary |
| ------ |
| 10000  |
| 10000  |
| 9000   |
| 8000   |

### Using RANK()

| salary | rank |
| ------ | ---- |
| 10000  | 1    |
| 10000  | 1    |
| 9000   | 3    |
| 8000   | 4    |

Notice rank **2 is skipped**.

---

### Using DENSE_RANK()

| salary | dense_rank |
| ------ | ---------- |
| 10000  | 1          |
| 10000  | 1          |
| 9000   | 2          |
| 8000   | 3          |

No gaps in ranking.

---

# Interview Summary

| Method                | Handles Duplicates | Returns Employees | Recommended      |
| --------------------- | ------------------ | ----------------- | ---------------- |
| ORDER BY + OFFSET     | ❌                  | ❌                 | No               |
| DISTINCT + OFFSET     | ✅                  | ❌                 | Sometimes        |
| DENSE_RANK() Subquery | ✅                  | ✅                 | Yes              |
| DENSE_RANK() CTE      | ✅                  | ✅                 | Best Readability |

**Most interviewers prefer `DENSE_RANK()` because it correctly handles duplicate salaries and can return the employee details along with the salary.**
