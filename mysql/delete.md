# DELETE Operations and Foreign Keys in MySQL

This document explains how DELETE operations work in MySQL, especially when foreign key constraints are involved.

## DELETE with Foreign Keys

If you try to delete a row from a parent table that is referenced by a child table through a foreign key, the deletion will fail by default.

### Example Scenario

Suppose you have:

```sql
CREATE TABLE worker (
    WORKER_ID INT PRIMARY KEY,
    FIRST_NAME VARCHAR(50),
    LAST_NAME VARCHAR(50),
    SALARY DECIMAL(10,2),
    JOINING_DATE DATETIME,
    DEPARTMENT VARCHAR(50)
);

CREATE TABLE title (
    WORKER_REF_ID INT,
    WORKER_TITLE VARCHAR(50) NOT NULL,
    AFFECTED_FROM DATETIME,
    FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
);
```

Attempting to delete a worker that has titles will fail:

```sql
DELETE FROM worker WHERE WORKER_ID = 1;
```

Error: `Cannot delete or update a parent row: a foreign key constraint fails`

## Handling Foreign Key Constraints

### Option 1: ON DELETE CASCADE

Automatically delete child rows when the parent is deleted.

```sql
CREATE TABLE title (
    WORKER_REF_ID INT,
    WORKER_TITLE VARCHAR(50) NOT NULL,
    AFFECTED_FROM DATETIME,
    FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
    ON DELETE CASCADE
);
```

Now, deleting a worker will also delete all related titles.

### Option 2: ON DELETE SET NULL

Set the foreign key to NULL when the parent is deleted.

```sql
ALTER TABLE title DROP FOREIGN KEY title_ibfk_1;

ALTER TABLE title
ADD CONSTRAINT title_ibfk_1
FOREIGN KEY (WORKER_REF_ID)
REFERENCES worker(WORKER_ID)
ON DELETE SET NULL;
```

After deletion:

```sql
DELETE FROM worker WHERE WORKER_ID = 1;
SELECT * FROM title WHERE WORKER_REF_ID IS NULL;
```

### Option 3: Manual Deletion

Delete child rows first, then the parent.

```sql
DELETE FROM title WHERE WORKER_REF_ID = 1;
DELETE FROM worker WHERE WORKER_ID = 1;
```

## Restoring Data

To add back a worker:

```sql
INSERT INTO worker (FIRST_NAME, LAST_NAME, SALARY, JOINING_DATE, DEPARTMENT)
VALUES ('Amit', 'Sharma', 450000, '2015-03-15 09:00:00', 'Admin');
```

To set the primary key (if needed):

```sql
UPDATE worker
SET WORKER_ID = 1
WHERE WORKER_ID = 11;
```

## Summary

- By default, foreign keys prevent deletion of referenced rows.
- Use `ON DELETE CASCADE` to auto-delete children.
- Use `ON DELETE SET NULL` to nullify foreign keys.
- Manually delete children before parents as an alternative.

Choose the option that fits your data integrity requirements.
