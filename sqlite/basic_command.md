# Basic SQL Commands in SQLite

This document provides a comprehensive cheat sheet of essential SQL commands you'll use frequently in SQLite.

## Database Commands

SQLite-specific commands for managing databases and connections:

```sql
.databases          -- Show attached databases
.tables             -- List all tables in the current database
.schema users       -- Show the structure of the "users" table
.quit               -- Exit SQLite CLI
PRAGMA table_info(users);  -- Show detailed column information for users table
```

## Table Commands

### Creating Tables

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE
);
```

### Dropping Tables

```sql
DROP TABLE users;   -- Delete a table and all its data
```

## Data Manipulation

### Inserting Data

```sql
INSERT INTO users (name, email) VALUES ('Ayush', 'ayush@example.com');
```

### Reading Data (SELECT)

```sql
SELECT * FROM users;               -- Select all rows and columns
SELECT name FROM users;            -- Select specific column
SELECT * FROM users WHERE id = 1;  -- Filter rows with conditions
```

### Updating Data

```sql
UPDATE users SET name = 'John' WHERE id = 1;
```

### Deleting Data

```sql
DELETE FROM users WHERE id = 2;
```

## Schema Modifications

### Adding Columns

```sql
ALTER TABLE users ADD COLUMN age INTEGER;   -- Add a new column
```

## Terminal Commands

- Clear terminal: `clear` or `Ctrl+L`

## Essential SQL Operations

The core SQL operations you must know:

- **CREATE**: Create tables and databases
- **INSERT**: Add new data
- **SELECT**: Retrieve data
- **UPDATE**: Modify existing data
- **DELETE**: Remove data
- **DROP**: Delete tables/databases
- **ALTER**: Modify table structure

## Additional Resources

For preprocessing commands and advanced methods, refer to W3Schools SQL documentation.

    Clear terminal → clear or Ctrl+L

    Must-know SQL: CREATE, INSERT, SELECT, UPDATE, DELETE, DROP, ALTER


    Refer to W3school for preprossing commands and methods
