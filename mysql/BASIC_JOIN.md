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

Use JOINs to combine data efficiently based on relationships between tables.
