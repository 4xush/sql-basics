# SQLite Fundamentals and Common Questions

## Tables Without Primary Keys

In SQLite (and SQL in general), a table can exist without a primary key:

```sql
CREATE TABLE products (
    name TEXT,
    price REAL
);
```

This is a valid table. However, without a primary key:

- **No uniqueness enforcement**: You could insert duplicate rows
- **No automatic indexing**: Lookups may be slower
- **No referential integrity**: Cannot be referenced by foreign keys

### Example of Duplicate Rows

```sql
INSERT INTO products VALUES ('Pen', 10.0);
INSERT INTO products VALUES ('Pen', 10.0);  -- This will succeed
```

Both rows will exist in the table.

## Why Primary Keys Are Important

1. **Uniqueness**: Prevents duplicate rows representing the same entity
2. **Indexing**: SQLite automatically creates an index on primary keys for faster lookups
3. **Relationships**: Foreign keys in other tables usually reference primary keys

## SQLite Quirks

### RowID

If you don't define a primary key, SQLite still has an internal hidden column called `rowid` (unless you explicitly use `WITHOUT ROWID`).

```sql
SELECT rowid, * FROM products;
```

This allows you to uniquely identify each row.

### Modifying Primary Keys

⚠️ **Important**: In SQLite, you cannot directly modify a column to make it a primary key. Instead, you need to:

1. Create a new table with the desired schema
2. Copy data from the old table
3. Drop the old table
4. Rename the new table

## SQLite CLI Headers

By default, the SQLite CLI only prints row values without headers. To enable headers:

```sql
.headers on
```

Now all SELECT queries will show column names.

## Case Sensitivity in SQLite

### IN Operator and Case Sensitivity

By default, the `IN` operator (and `=`) is case-sensitive in SQLite. So `'Amit'` is not equal to `'amit'`.

This is why `NOT IN ('amit', 'priya')` didn't exclude `'Amit'` and `'Priya'` - they have different cases.

### Making Comparisons Case-Insensitive

#### Method 1: Using LOWER()

Convert both the column and values to lowercase:

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
