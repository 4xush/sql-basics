Yes — if you try to delete a row from worker that is referenced by Title through a foreign key, it will fail by default in MariaDB/MySQL.

🔹 Why

You currently have a foreign key like:

CREATE TABLE Title (
WORKER_REF_ID INT,
WORKER_TITLE VARCHAR(50) NOT NULL,
AFFECTED_FROM DATETIME,
FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
);

    By default, foreign keys prevent deletion if there are child rows referencing the parent.

    So DELETE FROM worker WHERE WORKER_ID = 1; will fail if Title has WORKER_REF_ID = 1.

🔹 How to allow deletion

    CASCADE deletion (automatically delete child rows):

CREATE TABLE Title (
WORKER_REF_ID INT,
WORKER_TITLE VARCHAR(50) NOT NULL,
AFFECTED_FROM DATETIME,
FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
ON DELETE CASCADE
);

    Now, deleting a worker will also delete all their titles.

    Set foreign key to NULL (optional):

FOREIGN KEY (WORKER_REF_ID) REFERENCES worker(WORKER_ID)
ON DELETE SET NULL

    Deletes the worker, and sets WORKER_REF_ID in Title to NULL.

    Manual deletion

    First delete from Title:

DELETE FROM Title WHERE WORKER_REF_ID = 1;

    Then delete the worker:

DELETE FROM worker WHERE WORKER_ID = 1;

✅ Summary:

    Without ON DELETE CASCADE, deleting a referenced worker fails.

    With CASCADE or manual deletion, you can remove the worker safely.

Steps to change the foreign key in title to set WORKER_REF_ID to NULL instead of deleting the row when the referenced worker is deleted.

Drop the existing foreign key (ON DELETE CASCADE currently):

ALTER TABLE title DROP FOREIGN KEY title_ibfk_1;

    Add the foreign key back with ON DELETE SET NULL:

ALTER TABLE title
ADD CONSTRAINT title_ibfk_1
FOREIGN KEY (WORKER_REF_ID)
REFERENCES worker(WORKER_ID)
ON DELETE SET NULL;

✅ Now behavior:

    If you delete a worker that has entries in title, those title rows remain, but WORKER_REF_ID becomes NULL.

    Example:

DELETE FROM worker WHERE WORKER_ID = 1;
SELECT \* FROM title WHERE WORKER_REF_ID IS NULL;

Adding back to the worker :

INSERT INTO worker (FIRST_NAME, LAST_NAME, SALARY, JOINING_DATE, DEPARTMENT) VALUES ('Amit', 'Sharma', 450000, '2015-03-15 09:00:00',
'Admin');

and set the previous primary key

UPDATE worker
SET WORKER_ID = 1
WHERE WORKER_ID = 11;
