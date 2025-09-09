# Understanding the WHERE Clause in SQLite

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

By default, the `IN` operator (and `=`) is case-sensitive in SQLite. So `'Amit'` is not equal to `'amit'`. That's why `NOT IN ('amit', 'priya')` didn't exclude `'Amit'` and `'Priya'`.

### Making Comparisons Case-Insensitive

#### Method 1: Using LOWER()

Convert both the column and the values to lowercase:

```sql
SELECT *
FROM worker
WHERE LOWER(first_name) NOT IN ('amit', 'priya');
```

- `LOWER(first_name)` converts the name to lowercase
- `'amit'` and `'priya'` are already lowercase
- Now the comparison ignores case

#### Method 2: Using COLLATE NOCASE

SQLite supports NOCASE collation for case-insensitive comparisons:

```sql
SELECT *
FROM worker
WHERE first_name NOT IN ('amit', 'priya') COLLATE NOCASE;
```

This makes the `IN` comparison ignore case.

Both approaches will now correctly exclude `'Amit'` and `'Priya'` regardless of uppercase/lowercase.

### WHERE

```sql
SELECT MAX(salary)
    FROM worker
    WHERE salary < (SELECT MAX(salary) FROM worker)
```

**Here the WHERE CLAUSE will DISCARD the row which has greater salary then subQuery val, hence the table is filtered so select will find the max among the filtered one**