
In MariaDB (and MySQL), there are a few ways to get **Nth highest salary**.

---

## ✅ Method 1: Nested subqueries

For the **2nd highest** we already had:

```sql
SELECT MAX(salary)
FROM worker
WHERE salary < (SELECT MAX(salary) FROM worker);
```

For the **3rd highest**:

```sql
SELECT MAX(salary)
FROM worker
WHERE salary < (
    SELECT MAX(salary)
    FROM worker
    WHERE salary < (SELECT MAX(salary) FROM worker)
);
```



### Similarly 2nd highest salary using `NOT IN` by excluding the maximum salary and then taking the max of the rest.

Here’s the query:

```sql
SELECT MAX(salary) AS second_highest_salary
FROM worker
WHERE salary NOT IN (SELECT MAX(salary) FROM worker);
```

---

### How it works:

1. `SELECT MAX(salary) FROM worker` → gets the **highest salary** (500000 in your data → Meena).
2. `WHERE salary NOT IN (...)` → removes that max salary.
3. `MAX(salary)` on the remaining set → gives the **second highest salary** (470000 → Amitabh & Suresh).

---

### To get full details of employees with 2nd highest salary:

```sql
SELECT *
FROM worker
WHERE salary = (
    SELECT MAX(salary)
    FROM worker
    WHERE salary NOT IN (SELECT MAX(salary) FROM worker)
);
```

…and so on.
This works but becomes **ugly** when you go beyond a few levels.

---

## ✅ Method 2: `LIMIT` with `OFFSET`

Much cleaner:

```sql
SELECT DISTINCT salary
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET 0;   -- 1st highest

SELECT DISTINCT salary
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET 1;   -- 2nd highest

SELECT DISTINCT salary
FROM worker
ORDER BY salary DESC
LIMIT 1 OFFSET 2;   -- 3rd highest
```

⚡ This is the most common approach for Nth highest.

---

## ✅ Method 3: Correlated Subquery ranking trick

```
Check n no. of rows (inner query row) have maximum value than me (outer query row value) = nth rank ,each outer query row value get compare to every inner query's row
```
**At last count all rows of inner query :**

```sql
SELECT COUNT(salary)
  FROM worker w2
  WHERE w2.salary >= w1.salary
  ```
### Query:

```sql
SELECT w1.*
FROM worker w1
WHERE 3 = (
  SELECT COUNT(salary)
  FROM worker w2
  WHERE w2.salary >= w1.salary
);
```

---

### 🔎 How it works

* For each row `w1` in `worker`, the subquery counts **how many salaries are greater than or equal to `w1.salary`**.

* That count = **rank position (descending)** of that salary.

* Example with your data (ordered `salary desc`):

| Rank | Salary | Name    |
| ---- | ------ | ------- |
| 1    | 500000 | Meena   |
| 2    | 470000 | Amitabh |
| 2    | 470000 | Suresh  |
| 4    | 450000 | Amit    |
| 5    | 400000 | Anita   |
| 6    | 320000 | Kiran   |
| 7    | 300000 | Rohit   |
| 8    | 250000 | Priya   |
| 9    | 200000 | Arjun   |
| 10   | 80000  | Vikram  |

---

### ✅ When you do:

* `WHERE 3 = (...)` → You get everyone at **rank 3** (so 470000 → Amitabh & Suresh).
* `WHERE 4 = (...)` → You get rank 4 (450000 → Amit).

So this is basically a **manual ranking system using correlated subqueries**.

---

### 📌 Key Observations

* This avoids `ORDER BY LIMIT/OFFSET`.
* Handles ties automatically (you got **two people in rank 3** because both have salary = 470000).
* Works in MySQL/MariaDB/SQLite even if they don’t support `RANK()` window function.

---

## MISTAKE : Fetch last 5 record from table :


```sql
select * from worker order by worker_id limit 5 offset ((select count(worker_id) from worker)-5);
```
Ah, I see the issue 👇

Your query has two syntax mistakes:

1. You wrote **`form`** instead of **`from`**.
2. `LIMIT ... OFFSET ...` doesn’t accept a subquery directly unless wrapped properly.

Let’s fix it ✅

If you want the **last 5 rows ordered by worker\_id**, you can write:

```sql 

SELECT *
FROM (
    SELECT * 
    FROM worker
    ORDER BY worker_id DESC
    LIMIT 5
) AS sub
ORDER BY worker_id;

```

✅ This construct is called a **Derived Table (or Inline View)**.

* A *subquery in the `FROM` clause*.
* You can treat it as a temporary table generated at runtime.
* Then the outer query (`ORDER BY worker_id`) works on that derived table.

---