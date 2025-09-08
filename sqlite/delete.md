DELETE FROM worker;
here’s what happens:
All rows are deleted from the table worker.
The table still exists.
Its schema (columns, constraints, indexes) remain unchanged.
Only the data (records) is removed.
Table is now empty
If you do:
SELECT * FROM worker;
you’ll get no rows back.
Auto-increment (if any)
In SQLite, if your worker table has a column like:
id INTEGER PRIMARY KEY AUTOINCREMENT
then after DELETE FROM worker;, the counter does not reset by default.
The next inserted row will continue from the last highest ID.


Example:

Before delete → rows with IDs 1, 2, 3.

Run DELETE FROM worker;.

Insert a new row → ID will be 4, not 1.

like here after clearing - 

sqlite> select * from worker;
WORKER_ID|FIRST_NAME|LAST_NAME|SALARY|JOINING_DATE|DEPARTMENT
11|Amit|Sharma|450000|2015-03-15 09:00:00|Admin
12|Amitabh|Singh|500000|2014-02-20 09:00:00|Admin

the autoincrement started from 11 

If you want to reset the counter too, you’d need:

DELETE FROM worker;
DELETE FROM sqlite_sequence WHERE name='worker';
In short: DELETE FROM worker; clears all data but keeps the table structure.


You’re absolutely right to call me out here 🔥 — let’s clear this up properly.
🔹 Where I went wrong
I mentioned using
DELETE FROM sqlite_sequence WHERE name='worker';
to reset IDs. That advice only works when you use
id INTEGER PRIMARY KEY
(without AUTOINCREMENT).
👉 With AUTOINCREMENT, SQLite ignores that reset because it has a stricter guarantee:
Never reuse an ID once it’s been assigned, even if the row is deleted or the sequence is reset.
That’s why you still saw 41 instead of 1.
🔹 Correct understanding
Without AUTOINCREMENT
Deleting from sqlite_sequence (and running VACUUM) really does reset the next ID to 1.
With AUTOINCREMENT
The counter is not allowed to go backward.
Once 40 has been used, the next insert will always be ≥ 41.
Resetting sqlite_sequence does not override this behavior.
🔹 What you should do
If you want to truly restart IDs from 1 in SQLite:
Drop and recreate the table:
DROP TABLE worker; CREATE TABLE worker ( WORKER_ID INTEGER PRIMARY KEY AUTOINCREMENT, FIRST_NAME TEXT NOT NULL, LAST_NAME TEXT NOT NULL, SALARY INTEGER NOT NULL, JOINING_DATE TEXT NOT NULL, DEPARTMENT TEXT NOT NULL );
→ Now IDs start from 1.
Or, don’t use AUTOINCREMENT unless you need the guarantee of “never reuse IDs.



🔹 SQLite (AUTOINCREMENT)
INTEGER PRIMARY KEY AUTOINCREMENT:
Guarantees that rowids never repeat.
Even if you DELETE rows, the counter never goes back.
Once an ID is used, it is never reused.
Example:
Insert → IDs 1, 2, 3 DELETE all Next insert → 4
If you reset the sqlite_sequence table, SQLite may reuse numbers only if AUTOINCREMENT is NOT used.
With AUTOINCREMENT, it will always keep counting upwards.
🔹 MySQL (AUTO_INCREMENT)
AUTO_INCREMENT in MySQL is more flexible:
After DELETE, the counter does not reset automatically, but...
You can reset it manually:
ALTER TABLE worker AUTO_INCREMENT = 1;
→ Next insert will start from 1 (or the lowest available higher number if rows exist).
If you TRUNCATE TABLE worker;, MySQL resets the counter to 1 automatically.
MySQL can reuse IDs if you reset the counter and there’s no row with that ID.
🔹 Side-by-side Example
SQLite
CREATE TABLE worker ( id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT ); INSERT INTO worker (name) VALUES ('A'), ('B'), ('C'); -- IDs 1,2,3 DELETE FROM worker; -- delete all INSERT INTO worker (name) VALUES ('D'); -- ID 4 (not 1)
MySQL
CREATE TABLE worker ( id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(100) ); INSERT INTO worker (name) VALUES ('A'), ('B'), ('C'); -- IDs 1,2,3 DELETE FROM worker; -- delete all INSERT INTO worker (name) VALUES ('D'); -- ID 4 (not 1) ALTER TABLE worker AUTO_INCREMENT = 1; INSERT INTO worker (name) VALUES ('E'); -- ID 1 (reset works)
✅ Summary:
SQLite AUTOINCREMENT = IDs never reused (even after deletes).
MySQL AUTO_INCREMENT = IDs can be reset (via ALTER TABLE or TRUNCATE)


WHAT WILL HAPPEN IF THE TABLE IS HAVING REF OF ANOTHER TABLE AND THE PARENT GOT DELETED - 

Ahh, now that’s important ⚡ — you’ve got two tables:

worker (the parent table)

title (the child table, with worker_id as a foreign key referencing worker.id)

What happens when you run:


DELETE FROM worker;

It depends on how the foreign key constraint is defined in SQLite:
🔹 1. If you didn’t enable foreign keys (SQLite default is OFF unless you run PRAGMA foreign_keys = ON;):

    The DELETE works fine.

    Rows in title still exist, but their worker_id now points to non-existent workers → this causes orphaned rows.

🔹 2. If you enabled foreign keys with PRAGMA foreign_keys = ON;, then it depends on the ON DELETE rule you set when defining the foreign key:

    ON DELETE CASCADE
    → Deleting a worker row will automatically delete all rows in title with that worker_id.
    So DELETE FROM worker; clears both tables.

    ON DELETE SET NULL
    → The title.worker_id will be set to NULL when the parent is deleted.

    ON DELETE RESTRICT (default if nothing is specified)
    → SQLite will block the delete if there are rows in title referencing that worker.
    You’d get an error like:

    FOREIGN KEY constraint failed

✅ To check what’s happening in your DB:
Run this in SQLite:

PRAGMA foreign_keys;

    Returns 1 → foreign keys are enforced.

    Returns 0 → foreign keys are ignored.

And to see how the foreign key is defined:

.schema title


CREATE TABLE Title (
    WORKER_REF_ID INTEGER,
    WORKER_TITLE TEXT NOT NULL,
    AFFECTED_FROM DATETIME,
    FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
);
sqlite> 


✅ Best Practice

If you want automatic cleanup when deleting workers, define the table like this:

CREATE TABLE Title (
    WORKER_REF_ID INTEGER,
    WORKER_TITLE TEXT NOT NULL,
    AFFECTED_FROM DATETIME,
    FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID) ON DELETE CASCADE
);



