in SQLite (and SQL in general) a table can exist without a primary key.

CREATE TABLE products (
    name TEXT,
    price REAL
);


This is a valid table.

But since there’s no primary key:

Nothing enforces uniqueness of rows.

You could insert duplicate rows:

INSERT INTO products VALUES ('Pen', 10.0);
INSERT INTO products VALUES ('Pen', 10.0);

Both rows will exist.




Why primary keys are important

Uniqueness → Prevents duplicate rows that represent the same entity.

Indexing → Makes lookups faster (since SQLite auto-creates an index on the primary key).

Relationships → If you want foreign keys in other tables, they usually reference a primary key.




SQLite quirks

If you don’t define a primary key, SQLite still has an internal hidden column called rowid (unless you explicitly use WITHOUT ROWID).

That means you can still uniquely identify each row using rowid


⚠️ In SQLite, you cannot directly modify a column to make it a primary key.
Instead, the standard way is:


By default, the SQLite CLI only prints row values (no headers).
You need to enable headers.

.headers on

SQLite, the IN operator (and =) is case-sensitive by default. So 'Amit' is not equal to 'amit'. That’s why your NOT IN ('amit', 'priya') didn’t exclude 'Amit' and 'Priya'.

How to make it case-insensitive
1️⃣ Using LOWER()

Convert both the column and the values to lowercase:

SELECT *
FROM worker
WHERE LOWER(first_name) NOT IN ('amit', 'priya');

    LOWER(first_name) converts the name to lowercase.

    'amit' and 'priya' are already lowercase.

    Now the comparison ignores case.

2️⃣ Using COLLATE NOCASE

SQLite supports NOCASE collation for case-insensitive comparisons:

SELECT *
FROM worker
WHERE first_name NOT IN ('amit', 'priya') COLLATE NOCASE;

    This makes the IN comparison ignore case.

✅ Both approaches will now correctly exclude 'Amit' and 'Priya' regardless of uppercase/lowercase.