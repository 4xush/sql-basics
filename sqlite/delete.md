# DELETE Operations in SQLite

## Basic DELETE Statement

```sql
DELETE FROM worker;
```

### What happens:

- All rows are deleted from the table worker.
- The table still exists.
- Its schema (columns, constraints, indexes) remain unchanged.
- Only the data (records) is removed.
- Table is now empty

### Verification:

```sql
SELECT * FROM worker;
-- Returns no rows
```

## Auto-Increment Behavior

### With AUTOINCREMENT

If your table has:

```sql
id INTEGER PRIMARY KEY AUTOINCREMENT
```

After `DELETE FROM worker;`, the counter **does not reset** by default.

**Example:**

- Before delete → rows with IDs 1, 2, 3.
- After `DELETE FROM worker;`
- Next insert → ID will be 4, not 1.

### Resetting Auto-Increment Counter

#### Without AUTOINCREMENT

```sql
DELETE FROM worker;
DELETE FROM sqlite_sequence WHERE name='worker';
```

#### With AUTOINCREMENT

SQLite's `AUTOINCREMENT` guarantees that rowids never repeat. Even after deleting rows, the counter never goes backward.

**To truly restart IDs from 1:**

```sql
DROP TABLE worker;
CREATE TABLE worker (
    WORKER_ID INTEGER PRIMARY KEY AUTOINCREMENT,
    FIRST_NAME TEXT NOT NULL,
    LAST_NAME TEXT NOT NULL,
    SALARY INTEGER NOT NULL,
    JOINING_DATE TEXT NOT NULL,
    DEPARTMENT TEXT NOT NULL
);
```

## SQLite vs MySQL Auto-Increment

### SQLite (AUTOINCREMENT)

- `INTEGER PRIMARY KEY AUTOINCREMENT`
- Guarantees rowids never repeat
- Counter never goes backward
- Once an ID is used, it is never reused

**Example:**

```sql
CREATE TABLE worker (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT);
INSERT INTO worker (name) VALUES ('A'), ('B'), ('C'); -- IDs 1,2,3
DELETE FROM worker; -- Delete all
INSERT INTO worker (name) VALUES ('D'); -- ID 4 (not 1)
```

### MySQL (AUTO_INCREMENT)

- `AUTO_INCREMENT` is more flexible
- Counter can be reset manually
- Can reuse IDs after reset

**Example:**

```sql
CREATE TABLE worker (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(100));
INSERT INTO worker (name) VALUES ('A'), ('B'), ('C'); -- IDs 1,2,3
DELETE FROM worker; -- Delete all
ALTER TABLE worker AUTO_INCREMENT = 1;
INSERT INTO worker (name) VALUES ('E'); -- ID 1 (reset works)
```

## Foreign Key Constraints and DELETE

### Scenario

You have two tables:

- `worker` (parent table)
- `title` (child table with `worker_id` as foreign key)

### What happens with `DELETE FROM worker;`

#### Foreign Keys Disabled (Default)

```sql
PRAGMA foreign_keys;
-- Returns 0 (disabled)
```

- DELETE works fine
- Rows in `title` remain but become orphaned
- `worker_id` points to non-existent workers

#### Foreign Keys Enabled

```sql
PRAGMA foreign_keys = ON;
PRAGMA foreign_keys;
-- Returns 1 (enabled)
```

Behavior depends on the `ON DELETE` rule:

### ON DELETE CASCADE

```sql
CREATE TABLE title (
    WORKER_REF_ID INTEGER,
    WORKER_TITLE TEXT NOT NULL,
    AFFECTED_FROM DATETIME,
    FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID) ON DELETE CASCADE
);
```

- Deleting a worker automatically deletes all related rows in `title`

### ON DELETE SET NULL

```sql
FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID) ON DELETE SET NULL
```

- `title.worker_id` is set to NULL when parent is deleted

### ON DELETE RESTRICT (Default)

```sql
FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
```

- SQLite blocks the delete if child rows exist
- Error: `FOREIGN KEY constraint failed`

### Checking Foreign Key Status

```sql
PRAGMA foreign_keys;  -- Check if enabled
.schema title;        -- See foreign key definition
```

## Summary

- `DELETE FROM table;` removes all data but keeps table structure
- SQLite `AUTOINCREMENT` never reuses IDs
- MySQL `AUTO_INCREMENT` can be reset
- Foreign key behavior depends on `PRAGMA foreign_keys` setting and `ON DELETE` rules
- Use `ON DELETE CASCADE` for automatic cleanup of related data
