# SQL JOINs in MySQL

This document explains the different types of SQL JOINs in MySQL (and MariaDB). JOINs are used to combine rows from two or more tables based on a related column.

## Prerequisites

JOINs are applied when there is a common attribute (column) between tables. For example:

```sql
SELECT c.*, o.order_name
FROM customer c
JOIN orders o ON c.customer_id = o.customer_id;
```

This selects all columns from the `customer` table and the `order_name` column from the `orders` table.

## INNER JOIN

An INNER JOIN returns only the rows where there is a match in both tables based on the join condition.

### Example:

```sql
SELECT w.*, t.worker_title
FROM worker w
INNER JOIN title t ON w.worker_id = t.worker_ref_id;
```

### Result:

- Only rows with matching `worker_id` in both tables are returned.
- Rows without matches (e.g., worker_id 1 in the example) are excluded.

Sample output (truncated):

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE        | DEPARTMENT | worker_title |
| --------- | ---------- | --------- | ------ | ------------------- | ---------- | ------------ |
| 2         | Amitabh    | Singh     | 500000 | 2014-02-20 09:00:00 | Admin      | Sr. Manager  |
| ...       | ...        | ...       | ...    | ...                 | ...        | ...          |

## LEFT JOIN

A LEFT JOIN returns all rows from the left table and the matched rows from the right table. If no match, NULL is returned for right table columns.

### Example:

```sql
SELECT w.*, t.worker_title
FROM worker w
LEFT JOIN title t ON w.worker_id = t.worker_ref_id;
```

### Result:

- All rows from the left table (`worker`) are included.
- Unmatched rows show NULL in `worker_title`.

Sample output (truncated):

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE        | DEPARTMENT | worker_title |
| --------- | ---------- | --------- | ------ | ------------------- | ---------- | ------------ |
| 1         | Amit       | Sharma    | 450000 | 2015-03-15 09:00:00 | Admin      | NULL         |
| 2         | Amitabh    | Singh     | 500000 | 2014-02-20 09:00:00 | Admin      | Sr. Manager  |
| ...       | ...        | ...       | ...    | ...                 | ...        | ...          |

## RIGHT JOIN

A RIGHT JOIN returns all rows from the right table and the matched rows from the left table. If no match, NULL is returned for left table columns.

### Example:

```sql
SELECT w.*, t.worker_title
FROM worker w
RIGHT JOIN title t ON w.worker_id = t.worker_ref_id;
```

### Result:

- All rows from the right table (`title`) are included.
- Unmatched rows show NULL in worker columns.

Sample output (truncated):

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE        | DEPARTMENT | worker_title |
| --------- | ---------- | --------- | ------ | ------------------- | ---------- | ------------ |
| 2         | Amitabh    | Singh     | 500000 | 2014-02-20 09:00:00 | Admin      | Sr. Manager  |
| ...       | ...        | ...       | ...    | ...                 | ...        | ...          |

## FULL JOIN

A FULL JOIN returns all rows from both tables, with NULLs where there is no match. MySQL does not support FULL JOIN directly; use UNION of LEFT and RIGHT JOIN.

### Example:

```sql
SELECT w.*, t.worker_title
FROM worker w
LEFT JOIN title t ON w.worker_id = t.worker_ref_id
UNION
SELECT w.*, t.worker_title
FROM worker w
RIGHT JOIN title t ON w.worker_id = t.worker_ref_id;
```

### Result:

- All rows from both tables are included.
- Unmatched rows have NULLs.

Sample output (truncated):

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE        | DEPARTMENT | worker_title |
| --------- | ---------- | --------- | ------ | ------------------- | ---------- | ------------ |
| 1         | Amit       | Sharma    | 450000 | 2015-03-15 09:00:00 | Admin      | NULL         |
| 2         | Amitabh    | Singh     | 500000 | 2014-02-20 09:00:00 | Admin      | Sr. Manager  |
| ...       | ...        | ...       | ...    | ...                 | ...        | ...          |

## CARTESIAN JOIN (CROSS JOIN)

A CARTESIAN JOIN returns the Cartesian product of both tables, i.e., every row from the first table combined with every row from the second table.

### Example:

```sql
SELECT w.*, t.worker_title
FROM worker w, title t;
```

Or using CROSS JOIN:

```sql
SELECT w.*, t.worker_title
FROM worker w
CROSS JOIN title t;
```

### Result:

- If `worker` has 10 rows and `title` has 9 rows, the result has 90 rows (10 \* 9).
- This can produce a large number of rows and should be used cautiously.


## CARTESIAN JOIN (CROSS JOIN)

A CARTESIAN JOIN returns the Cartesian product of both tables, i.e., every row from the first table combined with every row from the second table.

### Example:

```sql
SELECT w.*, t.worker_title
FROM worker w, title t;
```

Or using CROSS JOIN:

```sql
SELECT w.*, t.worker_title
FROM worker w
CROSS JOIN title t;
```

### Result:

- If `worker` has 10 rows and `title` has 9 rows, the result has 90 rows (10 \* 9).
- This can produce a large number of rows and should be used cautiously.

Sample output (first few rows):

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE        | DEPARTMENT | worker_title |
| --------- | ---------- | --------- | ------ | ------------------- | ---------- | ------------ |
| 1         | Amit       | Sharma    | 450000 | 2015-03-15 09:00:00 | Admin      | Sr. Manager  |
| 1         | Amit       | Sharma    | 450000 | 2015-03-15 09:00:00 | Admin      | Lead         |
| ...       | ...        | ...       | ...    | ...                 | ...        | ...          |

## Summary

- **INNER JOIN**: Only matching rows.
- **LEFT JOIN**: All from left, matches from right.
- **RIGHT JOIN**: All from right, matches from left.
- **FULL JOIN**: All from both (use UNION in MySQL).
- **CROSS JOIN**: Cartesian product of both.


Perfect example 👍 you just ran a **correlated subquery ranking trick**. Let me explain what’s happening step by step:

---

### Query:

```sql
SELECT w1.*
FROM worker w1
WHERE 3 = (
  SELECT COUNT(salary)
  FROM worker w2
  WHERE w2.salary >= w1.salary
);
```

---

### 🔎 How it works

* For each row `w1` in `worker`, the subquery counts **how many salaries are greater than or equal to `w1.salary`**.

* That count = **rank position (descending)** of that salary.

* Example with your data (ordered `salary desc`):

| Rank | Salary | Name    |
| ---- | ------ | ------- |
| 1    | 500000 | Meena   |
| 2    | 470000 | Amitabh |
| 2    | 470000 | Suresh  |
| 4    | 450000 | Amit    |
| 5    | 400000 | Anita   |
| 6    | 320000 | Kiran   |
| 7    | 300000 | Rohit   |
| 8    | 250000 | Priya   |
| 9    | 200000 | Arjun   |
| 10   | 80000  | Vikram  |

---

### ✅ When you do:

* `WHERE 3 = (...)` → You get everyone at **rank 3** (so 470000 → Amitabh & Suresh).
* `WHERE 4 = (...)` → You get rank 4 (450000 → Amit).

So this is basically a **manual ranking system using correlated subqueries**.

For detailed explanation, see [Correlated Subqueries](./9CORRELATED_SUBQ.md).

---

### 📌 Key Observations

* This avoids `ORDER BY LIMIT/OFFSET`.
* Handles ties automatically (you got **two people in rank 3** because both have salary = 470000).
* Works in MySQL/MariaDB/SQLite even if they don’t support `RANK()` window function.

---

### IMPLICIT JOIN

 **writing a join but without explicitly using the `JOIN` keyword** (so basically the *old-style join* using `FROM` and `WHERE`).

---

### Example Table

```sql
worker
+------------+--------+---------+
| worker_id  | name   | dept_id |
+------------+--------+---------+
| 1          | Amit   | 10      |
| 2          | Suresh | 20      |
| 3          | Anita  | 10      |
+------------+--------+---------+

department
+---------+---------------+
| dept_id | dept_name     |
+---------+---------------+
| 10      | HR            |
| 20      | IT            |
+---------+---------------+
```

---

### 1️⃣ Normal `JOIN` syntax

```sql
SELECT w.worker_id, w.name, d.dept_name
FROM worker w
JOIN department d ON w.dept_id = d.dept_id;
```

---

### 2️⃣ Same thing **without JOIN**

This is the “implicit join” style:

```sql
SELECT w.worker_id, w.name, d.dept_name
FROM worker w, department d
WHERE w.dept_id = d.dept_id;
```

---

✅ Both queries produce:

```
+-----------+--------+-----------+
| worker_id | name   | dept_name |
+-----------+--------+-----------+
| 1         | Amit   | HR        |
| 2         | Suresh | IT        |
| 3         | Anita  | HR        |
+-----------+--------+-----------+
```

---

🔑 Notes:

* This **works the same** as `INNER JOIN`.
* But it’s considered **old style** SQL. The explicit `JOIN ... ON ...` is more readable, especially when mixing `LEFT/RIGHT JOIN`.
* Without the `WHERE`, you get a **Cartesian product** (every worker × every department).

---