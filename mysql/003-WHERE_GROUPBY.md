## WHERE - clause

The WHERE clause in SQL is used to filter records in a database based on specified conditions. It extracts only those rows from a table that fulfill the given criteria.
Key aspects of the WHERE clause:

```sql
SELECT MAX(salary)
    FROM worker
    WHERE salary < (SELECT MAX(salary) FROM worker)
```

**Here the WHERE clause will discard the row which has greater salary than the subQuery val, hence the table is filtered, So select will find the max among the filtered one**

SQL QUERY TO FETCH THE LIST OF EMPLOYEE HAVING SAME SALARY =

Let's first match some guy's salary -

```sql
UPDATE table_name
SET column_name = value
WHERE condition;
```

---

### Applying to worker :

```sql
UPDATE worker
SET salary = 250000
WHERE worker_id = 9;
```

---

### Your Query:

```sql
SELECT w1.*
FROM worker w1
INNER JOIN worker w2
ON w1.salary = w2.salary;
```

---

Nice one 👌 you just did a **self-join** on the same table.

### What happens:

- You joined the table `worker` with itself (`w1` and `w2`).
- The condition is `w1.salary = w2.salary`.
- That means **every row from w1 is matched with every row from w2 that has the same salary**.
- Since a row _always_ has the same salary as itself, each row matches at least once → duplicates appear.
- If multiple workers share the same salary (e.g., Amitabh & Suresh, both `470000`), then you get **multiple matches**.

---

### Example from your output:

1. **`Amitabh (470000)`** matches:

   - himself → row printed once.
   - `Suresh (470000)` → row printed again.

2. **`Priya (250000)`** matches:

   - herself.
   - `Vikram (250000)` → so Priya row printed twice.

3. **`Vikram (250000)`** also matches himself + Priya → printed twice.

---

### Why duplicates?

Because you didn’t exclude **self-joins** (w1 = w2).
Every row = itself + others with same salary.

---

### Fix: To avoid self-matching

If you want **only the pairs of different people with the same salary**, add a condition:

```sql
SELECT w1.FIRST_NAME, w1.SALARY, w2.FIRST_NAME AS Other_Person
FROM worker w1
INNER JOIN worker w2
  ON w1.salary = w2.salary
 AND w1.WORKER_ID < w2.WORKER_ID;
```

🔹 This way:

- `w1.WORKER_ID < w2.WORKER_ID` avoids duplicates and self-joins.
- You’ll get clean pairs like `(Amitabh, Suresh)` and `(Priya, Vikram)`.

### Another way to remove redundency -

```sql
SELECT w1.FIRST_NAME, w1.SALARY, w2.FIRST_NAME AS Other_Person
FROM worker w1
INNER JOIN worker w2
  ON w1.salary = w2.salary
 AND w1.WORKER_ID != w2.WORKER_ID;
```

## GROUP BY - clause

How to use **`GROUP BY`**, **`COUNT`**, and **`HAVING`** in MariaDB, using the `worker` table.

---

## Worker Table (Sample Data)

```sql
SELECT * FROM worker ORDER BY department;
```

| WORKER_ID | FIRST_NAME | LAST_NAME | SALARY | JOINING_DATE        | DEPARTMENT |
| --------- | ---------- | --------- | ------ | ------------------- | ---------- |
| 3         | Arjun      | Rao       | 200000 | 2021-04-20 09:15:00 | Account    |
| 7         | Rohit      | Mehra     | 300000 | 2017-01-10 09:00:00 | Account    |
| 1         | Amit       | Sharma    | 450000 | 2015-03-15 09:00:00 | Admin      |
| 2         | Amitabh    | Singh     | 470000 | 2014-02-20 09:00:00 | Admin      |
| 4         | Kiran      | Patel     | 320000 | 2021-12-01 09:00:00 | Admin      |
| 8         | Priya      | Gupta     | 250000 | 2018-11-05 11:00:00 | Admin      |
| 10        | Meena      | Chopra    | 500000 | 2020-09-12 10:00:00 | Finance    |
| 5         | Suresh     | Nair      | 470000 | 2022-08-17 14:00:00 | Finance    |
| 9         | Vikram     | Singh     | 80000  | 2019-06-18 09:00:00 | HR         |
| 6         | Anita      | Iyer      | 400000 | 2023-05-10 09:00:00 | HR         |

---

## 1️⃣ Count Without Grouping

```sql
SELECT department, COUNT(department) AS depCount
FROM worker;
```

📌 **Output**

| department | depCount |
| ---------- | -------- |
| Admin      | 10       |

👉 Since no `GROUP BY` was applied, the query counts **all rows** (10 workers) and shows only one row, picking the first department (`Admin`) arbitrarily.

---

## 2️⃣ Count With Grouping

```sql
SELECT department, COUNT(department) AS depCount
FROM worker
GROUP BY department;
```

📌 **Output**

| department | depCount |
| ---------- | -------- |
| Account    | 2        |
| Admin      | 4        |
| Finance    | 2        |
| HR         | 2        |

👉 Now we grouped by `department`. Each department is shown with the number of employees in it.

---

## 3️⃣ Filtering Groups Using HAVING

```sql
SELECT department, COUNT(department) AS depCount
FROM worker
GROUP BY department
HAVING depCount = 2;
```

📌 **Output**

| department | depCount |
| ---------- | -------- |
| Account    | 2        |
| Finance    | 2        |
| HR         | 2        |

👉 `HAVING` is used to filter grouped results (unlike `WHERE`, which filters rows before grouping).
Here we show only those departments that have exactly **2 employees**.

---

## 🔑 Key Takeaways

- `COUNT(column)` → counts non-NULL rows in that column.
- `GROUP BY column` → groups rows by column values.
- `HAVING` → filters results **after grouping**.
- `WHERE` → filters rows **before grouping**.

---

```sql
SELECT MAX(w2.salary), w2.department
FROM worker w2
GROUP BY w2.department;
```

| max(w2.salary) | department |
| -------------- | ---------- |
| 300000         | Account    |
| 470000         | Admin      |
| 500000         | Finance    |
| 400000         | HR         |


### Q. Fetch workers having maximum salary in their department : 

#### Wrong query : 

 ```sql
   SELECT w1.salary, w1.department
   FROM (
      SELECT MAX(w2.salary) AS max_salary, w2.department
      FROM worker w2
      GROUP BY w2.department
   ) AS alias
   INNER JOIN worker w1
      ON alias.max_salary = w1.salary
      AND alias.department = w1.department;
   ```

Yes ✅ you’ve got it almost right. Let me refine the idea:

---

### 🔹 How `GROUP BY` works

1. SQL first **groups rows** based on the column(s) you mention in `GROUP BY`.

   - In your case:
   → all rows are split into 4 groups: _Account, Admin, Finance, HR_.

2. Then you can apply **aggregate functions** (`MAX`, `MIN`, `SUM`, `COUNT`, `AVG`, etc.) on each group.

   - For example:

     ```sql
     MAX(salary)
     ```

     → looks **inside each group** and returns a single value (the highest salary in that group).

3. So the result has **one row per group**, not a single row overall.

   - That’s why your output has 4 rows (one per department).

---

### Your example

```sql
SELECT MAX(w2.salary), w2.department
FROM worker w2
GROUP BY department;
```

🔹 Steps:

- **Group 1 → Account**: {200000, 300000} → MAX = 300000
- **Group 2 → Admin**: {450000, 470000, 320000, 250000} → MAX = 470000
- **Group 3 → Finance**: {470000, 500000} → MAX = 500000
- **Group 4 → HR**: {400000, 250000} → MAX = 400000

✅ Output = 4 rows (one for each group).

---

### 🔑 Key Point

- `GROUP BY` **does not always give a single row**.
- It gives **one row per group**.
- The “single row” idea applies **inside each group**: aggregate functions reduce many rows of that group into one value.

---

### Mistake :

```sql
select w1.salary , w1.department from (select max(w2.salary), w2.department from worker w2 group by department) as alias inner join worker w1 on alias.salary = w1.salary and alias.department = w1.department;
```

I see what you’re trying to do 👍 — you want to **get the full details of the employee(s) who have the maximum salary in each department**.

But the query you wrote has a **syntax error** because of you didn’t give a proper column alias name for `MAX(w2.salary)`.

---

### ✅ Correct query

```sql
SELECT w1.salary, w1.department, w1.first_name, w1.last_name, w1.worker_id
FROM (
    SELECT MAX(w2.salary) AS max_salary, w2.department
    FROM worker w2
    GROUP BY w2.department
) AS alias
INNER JOIN worker w1
    ON alias.max_salary = w1.salary
   AND alias.department = w1.department;
```

---

### 🔹 Explanation

1. Subquery:

   ```sql
   SELECT MAX(w2.salary) AS max_salary, w2.department
   FROM worker w2
   GROUP BY w2.department;
   ```

   → gives you the **highest salary per department**:

   ```
   +------------+------------+
   | max_salary | department |
   +------------+------------+
   |     300000 | Account    |
   |     470000 | Admin      |
   |     500000 | Finance    |
   |     400000 | HR         |
   +------------+------------+
   ```

2. Outer query joins this with the main `worker` table to fetch **all details of those top earners**.

---

### ✅ Final Output (for your dataset)

```
+--------+------------+------------+-----------+-----------+
| salary | department | first_name | last_name | worker_id |
+--------+------------+------------+-----------+-----------+
| 300000 | Account    | Rohit      | Mehra     |         7 |
| 470000 | Admin      | Amitabh    | Singh     |         2 |
| 470000 | Admin      | Suresh     | Nair      |         5 |
| 500000 | Finance    | Meena      | Chopra    |        10 |
| 400000 | HR         | Anita      | Iyer      |         6 |
+--------+------------+------------+-----------+-----------+
```

---
