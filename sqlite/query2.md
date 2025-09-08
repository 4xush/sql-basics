# Advanced SQLite Queries

## Finding Top N Salaries

### Common Error: Incorrect LIMIT Syntax

```sql
-- This will cause an error:
SELECT * FROM worker ORDER BY salary DESC LIMIT = 5;
-- Parse error: near "=": syntax error
```

### Correct Syntax

```sql
SELECT * FROM worker ORDER BY salary DESC LIMIT 5;
```

**Output:**

```
WORKER_ID|FIRST_NAME|LAST_NAME|SALARY|JOINING_DATE|DEPARTMENT
2|Amitabh|Singh|500000|2014-02-20 09:00:00|Admin
10|Meena|Chopra|500000|2020-09-12 10:00:00|Finance
1|Amit|Sharma|450000|2015-03-15 09:00:00|Admin
6|Anita|Iyer|400000|2023-05-10 09:00:00|HR
4|Kiran|Patel|320000|2021-12-01 09:00:00|Admin
```

## Finding Nth Highest Salary

### Using OFFSET

To find the 5th highest salary:

```sql
SELECT *
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET 4;
```

### How it works:

- `ORDER BY salary DESC` - Sort salaries in descending order
- `LIMIT 1` - Return only 1 row
- `OFFSET 4` - Skip the first 4 rows (1st, 2nd, 3rd, 4th highest)

### General Formula

For Nth highest salary:

```sql
SELECT *
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET (N-1);
```

### Examples:

- 2nd highest: `LIMIT 1 OFFSET 1`
- 3rd highest: `LIMIT 1 OFFSET 2`
- 5th highest: `LIMIT 1 OFFSET 4`

## Alternative Methods

### Using Subquery

```sql
SELECT MAX(salary)
FROM worker
WHERE salary < (SELECT MAX(salary) FROM worker);
```

This finds the 2nd highest salary.

### Using DISTINCT and LIMIT

```sql
SELECT DISTINCT salary
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET 4;
```

This finds the 5th highest unique salary value.

## Summary

- Use `LIMIT N` to get top N records
- Use `LIMIT 1 OFFSET (N-1)` to get Nth record
- No `=` sign in LIMIT clause
- OFFSET starts from 0
- For unique values, use `DISTINCT` with salary
