# Case Sensitivity in MySQL

In MySQL (and MariaDB), case sensitivity depends on the operating system and the type of object.

## Database and Table Names

- **On Linux/Unix (e.g., Fedora)**: Database and table names are **case-sensitive** because they correspond to actual files in the filesystem.
  - `Title` ≠ `title` ≠ `TITLE`
- **On Windows/macOS**: They are usually case-insensitive.

### Example

If your table is named `Title` (with capital T), the following will work:

```sql
SELECT * FROM Title;
```

But this will fail:

```sql
SELECT * FROM title;
```

Error: `Table 'database.title' doesn't exist`

## Column Names

Column names are case-insensitive in MySQL, regardless of the OS.

- `first_name` = `FIRST_NAME` = `First_Name`

## String Values

String comparisons in WHERE clauses are case-insensitive by default.

- `WHERE name = 'John'` matches 'john', 'JOHN', etc.

To make them case-sensitive, use `COLLATE` or `BINARY`.

### Case-Sensitive String Comparison

```sql
SELECT * FROM worker WHERE first_name = 'John' COLLATE utf8_bin;
```

Or:

```sql
SELECT * FROM worker WHERE BINARY first_name = 'John';
```

## Summary

- **Table/Database Names**: Case-sensitive on Linux, insensitive on Windows.
- **Column Names**: Case-insensitive.
- **String Values**: Case-insensitive by default; use `COLLATE` or `BINARY` for sensitivity.

Always check your OS and MySQL configuration for exact behavior.
