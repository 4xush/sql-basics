# REPLACE vs INSERT in MySQL

This document explains the difference between `REPLACE` and `INSERT` statements in MySQL.

## INSERT Statement

The `INSERT` statement is used to add new rows to a table.

### Syntax:

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

### Behavior:

- Adds a new row if the primary key does not exist.
- Throws an error if a duplicate primary key is inserted (unless `ON DUPLICATE KEY UPDATE` is used).

### Example:

```sql
INSERT INTO worker (worker_id, first_name, last_name, salary)
VALUES (1, 'John', 'Doe', 50000);
```

This inserts a new row with `worker_id = 1`.

## REPLACE Statement

The `REPLACE` statement is used to insert or update rows.

### Syntax:

```sql
REPLACE INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);
```

### Behavior:

- If the row exists (based on primary key), it replaces the entire row. Unspecified columns are set to NULL.
- If the row does not exist, it inserts a new row.
- Requires a primary key or unique index.

### Example:

```sql
REPLACE INTO worker (worker_id, first_name, last_name, salary)
VALUES (1, 'Jane', 'Smith', 60000);
```

- If `worker_id = 1` exists, it updates the row with new values and sets other columns to NULL.
- If `worker_id = 1` does not exist, it inserts a new row.

## Key Differences

- **INSERT**: Adds new rows only. Fails on duplicate keys.
- **REPLACE**: Inserts or replaces existing rows. Can set unspecified columns to NULL.

Use `REPLACE` cautiously as it can overwrite data unintentionally.
