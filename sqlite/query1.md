# Useful SQLite Queries and Examples

## String Concatenation

### Concatenating Names

```sql
SELECT CONCAT(first_name, ' ', last_name) AS fullName FROM worker;
```

**Output:**

```
fullName
Amit Sharma
Amitabh Singh
Arjun Rao
Kiran Patel
Suresh Nair
Anita Iyer
Rohit Mehra
Priya Gupta
Vikram Singh
Meena Chopra
```

## Case Conversion

### Converting to Uppercase

```sql
SELECT UPPER(first_name) AS upper_title FROM worker_clone;
```

**Output:**

```
upper_title
AMIT
AMITABH
ARJUN
KIRAN
SURESH
ANITA
ROHIT
PRIYA
VIKRAM
MEENA
```

## Aggregation and Grouping

### Count by Department

```sql
SELECT department, COUNT(worker_id) AS worker_count
FROM worker
GROUP BY department
ORDER BY worker_count DESC;
```

**Output:**

```
DEPARTMENT|worker_count
Admin|4
HR|2
Finance|2
Account|2
```

## Date and Time Functions

### Current Date

```sql
SELECT DATE('now') AS current_date;
```

**Output:**

```
2025-09-07
```

### Current Time

```sql
SELECT TIME('now') AS current_time;
```

**Output:**

```
20:02:28
```

### Date Filtering (SQLite vs MySQL)

```sql
-- This works in MySQL:
SELECT * FROM worker WHERE YEAR(joining_date) = 2021 AND MONTH(joining_date) = 02;

-- In SQLite, use strftime():
SELECT * FROM worker WHERE strftime('%Y', joining_date) = '2021' AND strftime('%m', joining_date) = '02';
```

## JOIN Operations

### INNER JOIN with Pattern Matching

```sql
SELECT w.worker_id, w.first_name, t.worker_title
FROM worker w
JOIN title t ON w.worker_id = t.worker_ref_id
WHERE t.worker_title LIKE '%manager%';
```

**Output:**

```
WORKER_ID|FIRST_NAME|WORKER_TITLE
1|Amit|Manager
2|Amitabh|Sr. Manager
4|Kiran|Asst. Manager
6|Anita|Manager
10|Meena|Finance Manager
```

## HAVING Clause

### Group Filtering

```sql
SELECT t.worker_title, COUNT(*) AS no_of_worker
FROM title t
GROUP BY t.worker_title
HAVING no_of_worker > 1;
```

**Output:**

```
WORKER_TITLE|no_of_worker
Executive|2
Lead|2
Manager|2
```

## Mathematical Expressions

### Modulo Operation

```sql
SELECT * FROM worker w WHERE w.worker_id % 2 != 0;
-- Or: WHERE MOD(w.worker_id, 2) != 0;
```

**Output:**

```
WORKER_ID|FIRST_NAME|LAST_NAME|SALARY|JOINING_DATE|DEPARTMENT
1|Amit|Sharma|450000|2015-03-15 09:00:00|Admin
3|Arjun|Rao|200000|2021-04-20 09:15:00|Account
5|Suresh|Nair|150000|2022-08-17 14:00:00|Finance
7|Rohit|Mehra|300000|2017-01-10 09:00:00|Account
9|Vikram|Singh|80000|2019-06-18 09:00:00|HR
```

## Table Cloning

### Clone Schema Only

```sql
CREATE TABLE worker_clone AS
SELECT * FROM worker WHERE 0;
```

### Clone with Data

```sql
INSERT INTO worker_clone SELECT * FROM worker;
```

## Set Operations

### Finding Common Records (Intersection)

```sql
SELECT worker.* FROM worker
INNER JOIN worker_clone USING(worker_id);
```

### Finding Missing Records (Difference)

```sql
SELECT * FROM worker w
LEFT JOIN worker_clone c ON w.worker_id = c.worker_id
WHERE c.worker_id IS NULL;
```

## NULL Handling

### Finding NULL Values

```sql
SELECT * FROM worker w
LEFT JOIN worker_clone c ON w.worker_id = c.worker_id
WHERE c.worker_id IS NULL;
```

## Summary

- Use `CONCAT()` or `||` for string concatenation in SQLite
- `UPPER()` and `LOWER()` for case conversion
- `GROUP BY` with `HAVING` for filtered aggregations
- `strftime()` for date operations in SQLite (not `YEAR()`/`MONTH()`)
- `LIKE` with wildcards for pattern matching
- `IS NULL` to find missing relationships
- Table cloning with `CREATE TABLE AS` and conditional `WHERE`
