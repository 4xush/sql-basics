# Getting Started with SQLite CLI

## SQLite Version Check

To check your SQLite version:

```bash
sqlite3 --version
```

## Opening a Database

### Creating a New Database

```bash
sqlite3 mydb.sqlite
```

This command tells SQLite to:

- Open the database file `mydb.sqlite` if it exists
- Create a new database file if it doesn't exist
- Work in the current directory

### Important Note

SQLite creates the database file immediately, but doesn't write it to disk until you create something inside it (like a table or insert data).

## Creating Your First Table

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL
);
```

## Viewing Tables

To see all tables in your database:

```sql
.tables
```

## Exiting SQLite

To quit the SQLite CLI:

```sql
.quit
```

## Common Pitfall: Temporary vs Persistent Database

### The Problem

If you run `sqlite3` without a filename:

```bash
sqlite3
```

This creates a **temporary in-memory database** that is lost when you exit.

### The Solution

Always specify your database file:

```bash
sqlite3 mydb.sqlite
```

Now verify you're in the correct database:

```sql
.tables
```

### Key Difference

- `sqlite3` → Temporary empty database (lost on exit)
- `sqlite3 mydb.sqlite` → Persistent database saved in your folder

Your database file `mydb.sqlite` remains safe in your working directory.
