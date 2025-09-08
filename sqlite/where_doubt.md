# Understanding the WHERE Clause in SQLite

## Common Mistake: Using WHERE Incorrectly

A common mistake is to use the WHERE clause with an incorrect condition or syntax. For example:

```sql
SELECT * FROM products WHERE price;
```

This command is wrong because `price` alone is not a valid condition. The WHERE clause expects a boolean expression (e.g., `price > 10`).

**Correct usage:**

```sql
SELECT * FROM products WHERE price > 10;
```

## How Does WHERE Work?

The WHERE clause filters rows based on a condition. Only rows where the condition is true are returned.

## Common Error: Using IN with a Single String

If you use the IN clause incorrectly, SQLite may throw an error. For example:

```sql
SELECT * FROM worker WHERE first_name IN 'amit';
```

This is **wrong**. Here, `'amit'` is treated as a table name, not a value list, so you get:

```
Parse error: no such table: amit
```

**Correct usage:**

```sql
SELECT * FROM worker WHERE first_name IN ('amit', 'priya');
```

Here, `('amit', 'priya')` is a value list, so the query works as expected.

## When to Use IN

Use the `IN` operator when you want to check if a column's value matches any value in a list. It is useful for filtering rows based on multiple possible values.

**Example:**

```sql
SELECT * FROM worker WHERE first_name IN ('amit', 'priya', 'john');
```

This returns all rows where `first_name` is either 'amit', 'priya', or 'john'.

`IN` can also be used with a subquery:

```sql
SELECT * FROM worker WHERE department_id IN (SELECT id FROM department WHERE location = 'Delhi');
```

This returns all workers in departments located in Delhi.

## Is IN Case Sensitive?

- If you want case-sensitive matching, you can use the `COLLATE` clause:

```sql
SELECT * FROM worker WHERE first_name IN ('amit', 'priya') COLLATE binary;
```

This will only match exact case.

### Case Sensitivity in WHERE

- **Exact Match:**
  - The `=` operator matches the entire string (not substrings). For partial matches, use `LIKE` or `GLOB`.

### Examples

```sql
-- Case-insensitive match
SELECT * FROM products WHERE name = 'pen';

-- Partial match (case-insensitive)
SELECT * FROM products WHERE name LIKE '%pen%';

-- Case-sensitive match (after enabling case sensitivity)
PRAGMA case_sensitive_like = ON;
SELECT * FROM products WHERE name LIKE 'Pen';
```

## Summary

SQLite, the IN operator (and =) is case-sensitive by default. So 'Amit' is not equal to 'amit'. That’s why your NOT IN ('amit', 'priya') didn’t exclude 'Amit' and 'Priya'.

How to make it case-insensitive
1️⃣ Using LOWER()

Convert both the column and the values to lowercase:

SELECT \*
FROM worker
WHERE LOWER(first_name) NOT IN ('amit', 'priya');

    LOWER(first_name) converts the name to lowercase.

    'amit' and 'priya' are already lowercase.

    Now the comparison ignores case.

2️⃣ Using COLLATE NOCASE

SQLite supports NOCASE collation for case-insensitive comparisons:

SELECT \*
FROM worker
WHERE first_name NOT IN ('amit', 'priya') COLLATE NOCASE;

    This makes the IN comparison ignore case.

✅ Both approaches will now correctly exclude 'Amit' and 'Priya' regardless of uppercase/lowercase.

## Common Error: Using YEAR() and MONTH() Functions in SQLite

If you try to use `YEAR()` and `MONTH()` functions like in MySQL, SQLite will throw an error:

```sql
SELECT * FROM worker WHERE YEAR(joining_date) = 2021 AND MONTH(joining_date) = 2;
```

This results in:

```
Parse error: no such function: year
```

**Why does this happen?**

- SQLite does not have built-in `YEAR()` and `MONTH()` functions like MySQL or other databases.
- These functions are specific to MySQL and are not available in SQLite.

**In MySQL:**

In MySQL, these functions work directly:

```sql
SELECT * FROM worker WHERE YEAR(joining_date) = 2021 AND MONTH(joining_date) = 2;
```

This will work without errors in MySQL.

**How to fix it in SQLite:**

Use the `strftime()` function to extract date parts:

- For year: `strftime('%Y', joining_date)`
- For month: `strftime('%m', joining_date)`

**Correct usage:**

```sql
SELECT * FROM worker WHERE strftime('%Y', joining_date) = '2021' AND strftime('%m', joining_date) = '02';
```

Note that `strftime()` returns strings, so compare with strings ('2021', '02').

If you need to compare as numbers, you can cast them:

```sql
SELECT * FROM worker WHERE CAST(strftime('%Y', joining_date) AS INTEGER) = 2021 AND CAST(strftime('%m', joining_date) AS INTEGER) = 2;
```

## Summary
