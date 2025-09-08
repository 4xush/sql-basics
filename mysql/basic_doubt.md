In MySQL/MariaDB, case sensitivity depends on what you’re talking about:

1. 🔹 Database and Table Names

On Linux/Unix (like Fedora), table names and database names ARE case-sensitive, because they’re stored as actual files in the filesystem.

So Title ≠ title ≠ TITLE.

On Windows/macOS, they are usually case-insensitive.

👉 In your case (Fedora), since you see both Title and worker, if you try:

SELECT * FROM title;

you’ll get an error (because the table is Title with capital T).