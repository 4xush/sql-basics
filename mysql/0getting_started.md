🔑 Connect & Exit

mysql -u root -p       # login as root (you’ll be asked for password)
system clear;          #clear MariaDB shell
exit;                  # quit MariaDB shell

📂 Database (Schema) Management

SHOW DATABASES;                         -- list all databases
CREATE DATABASE mydb;                   -- create a new database
DROP DATABASE mydb;                     -- delete a database
USE mydb;                               -- switch/select a database
SELECT DATABASE();                      -- show current database in use


system clear;   --  clear terminal shell


📑 Tables

SHOW TABLES;                            -- list tables in current DB
DESCRIBE worker;                        -- view table structure (columns, types)
SHOW CREATE TABLE worker\G              -- full CREATE TABLE statement
DROP TABLE worker;                      -- delete a table

🗂️ Data Operations

SELECT * FROM worker;                   -- show all rows
SELECT FIRST_NAME, SALARY FROM worker;  -- select specific columns
INSERT INTO worker (FIRST_NAME, LAST_NAME, SALARY, JOINING_DATE, DEPARTMENT)
VALUES ('John','Doe',100000,'2024-01-01 09:00:00','IT');

UPDATE worker 
SET SALARY = 120000 
WHERE FIRST_NAME = 'John';

DELETE FROM worker 
WHERE FIRST_NAME = 'John';

🔍 Filtering, Sorting, Limits

SELECT * FROM worker WHERE DEPARTMENT = 'HR';        -- filter rows
SELECT * FROM worker ORDER BY SALARY DESC;           -- sort rows
SELECT * FROM worker LIMIT 5;                        -- first 5 rows

🔗 User & Privileges (Admin stuff)

CREATE USER 'ayush'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON mydb.* TO 'ayush'@'localhost';
FLUSH PRIVILEGES;