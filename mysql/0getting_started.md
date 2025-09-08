# Getting Started with MySQL

This guide provides an overview of installing, configuring, and using MySQL on a Linux system (Fedora). It covers basic operations for databases, tables, and data manipulation.

## 1. Installing MySQL on Fedora

To install MySQL server and client on Fedora, run the following command:

```bash
sudo dnf install @mysql
```

This installs the MySQL server (`mysqld`) and client (`mysql`).

After installation, start and enable the MySQL service:

```bash
sudo systemctl start mysqld
sudo systemctl enable mysqld
```

## 2. Accessing MySQL

To access MySQL, use the `mysql` command instead of `sqlite3`:

```bash
mysql -u root -p
```

- `-u root`: Log in as the root user.
- `-p`: Prompt for the password.

You will enter the MySQL shell:

```
mysql>
```

## 3. Creating and Using Databases

In MySQL, databases are created within the server, unlike SQLite where the database is a file.

### Key Differences:

- **SQLite**: File-based, no server required.
- **MySQL**: Server-based; connect via the `mysql` client.

### Commands:

```sql
CREATE DATABASE company;
USE company;
```

## 4. Connecting and Exiting

### Connect to MySQL:

```bash
mysql -u root -p
```

### Clear the Shell:

```sql
system clear;
```

### Exit the Shell:

```sql
exit;
```

## 5. Database (Schema) Management

- List all databases:

  ```sql
  SHOW DATABASES;
  ```

- Create a new database:

  ```sql
  CREATE DATABASE mydb;
  ```

- Delete a database:

  ```sql
  DROP DATABASE mydb;
  ```

- Switch to a database:

  ```sql
  USE mydb;
  ```

- Show the current database:
  ```sql
  SELECT DATABASE();
  ```

## 6. Table Management

- List tables in the current database:

  ```sql
  SHOW TABLES;
  ```

- View table structure:

  ```sql
  DESCRIBE worker;
  ```

- Show the full CREATE TABLE statement:

  ```sql
  SHOW CREATE TABLE worker\G
  ```

- Delete a table:
  ```sql
  DROP TABLE worker;
  ```

## 7. Data Operations

- Select all rows:

  ```sql
  SELECT * FROM worker;
  ```

- Select specific columns:

  ```sql
  SELECT FIRST_NAME, SALARY FROM worker;
  ```

- Insert a new row:

  ```sql
  INSERT INTO worker (FIRST_NAME, LAST_NAME, SALARY, JOINING_DATE, DEPARTMENT)
  VALUES ('John', 'Doe', 100000, '2024-01-01 09:00:00', 'IT');
  ```

- Update existing data:

  ```sql
  UPDATE worker
  SET SALARY = 120000
  WHERE FIRST_NAME = 'John';
  ```

- Delete data:
  ```sql
  DELETE FROM worker
  WHERE FIRST_NAME = 'John';
  ```

## 8. Filtering, Sorting, and Limiting Results

- Filter rows:

  ```sql
  SELECT * FROM worker WHERE DEPARTMENT = 'HR';
  ```

- Sort rows:

  ```sql
  SELECT * FROM worker ORDER BY SALARY DESC;
  ```

- Limit results:
  ```sql
  SELECT * FROM worker LIMIT 5;
  ```

## 9. User and Privileges Management

- Create a new user:

  ```sql
  CREATE USER 'ayush'@'localhost' IDENTIFIED BY 'mypassword';
  ```

- Grant privileges:

  ```sql
  GRANT ALL PRIVILEGES ON mydb.* TO 'ayush'@'localhost';
  ```

- Apply changes:
  ```sql
  FLUSH PRIVILEGES;
  ```

This guide covers the basics to get you started with MySQL. For more advanced topics, refer to the official MySQL documentation.
