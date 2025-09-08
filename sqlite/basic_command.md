📖 Basic SQL Commands (Cheat Sheet)

Here’s a solid starter pack you’ll use most of the time:

1. Database Commands (SQLite-specific)

.databases   -- show attached databases
.tables      -- list all tables
.schema users -- show structure of "users" table
.quit        -- exit SQLite
PRAGMA table_info(users); -- 


2. Table Commands

CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE
);

DROP TABLE users;   -- delete a table


3. Insert Data

INSERT INTO users (name, email) VALUES ('Ayush', 'ayush@example.com');


4. Read Data (SELECT)


SELECT * FROM users;               -- select all rows
SELECT name FROM users;            -- select specific column
SELECT * FROM users WHERE id = 1;  -- filter rows


5. Update Data

UPDATE users SET name = 'John' WHERE id = 1;

6. Delete Data

DELETE FROM users WHERE id = 2;

7. Other Useful

ALTER TABLE users ADD COLUMN age INTEGER;   -- add a new column

⚡ Summary:

    Clear terminal → clear or Ctrl+L

    Must-know SQL: CREATE, INSERT, SELECT, UPDATE, DELETE, DROP, ALTER


    Refer to W3school for preprossing commands and methods