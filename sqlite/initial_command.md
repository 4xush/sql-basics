sqlite3 --version

sqlite3 mydb.sqlite

This tells SQLite:

“Open (or create if it doesn’t exist) a database file called mydb.sqlite in the current directory.”


SQLite opened (or created) a database file named mydb.sqlite. But SQLite doesn’t actually write the file until you create something inside it (like a table, or insert data).


CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL
);

To view all tables in mydb.sqlite
.tables
 

Quit SQLite:

.quit



Your real DB (mydb.sqlite) is still safe in ~/Desktop/sql, but you didn’t open it.

✅ How to fix

Exit (.quit), then open your actual file:

sqlite3 mydb.sqlite

Now check:

.tables

Key Tip

sqlite3 → temporary empty DB (lost on exit).

sqlite3 mydb.sqlite → persistent database saved in your folder.