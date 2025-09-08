### What is a Subquery?

A subquery, also known as an inner query or a nested query, is a query that is nested inside another SQL query.

* Two types:

  * **Non-correlated subquery** → runs once, result is used by outer query.
  * **Correlated subquery** → runs once **for every row** of the outer query (like a loop).

---

The simplest way to think about it is as a **two-step question**. You need the answer to an inner question before you can even ask the outer question.

**The Golden Rule:** The subquery (the inner query) **always runs first**. Its result is then used by the outer query.

### The Classic "Average Salary" Problem

This is the most fundamental example and the perfect place to start.

**The Goal:** Find the names and salaries of all employees who earn more than the company's average salary.

This is a two-step problem:

1.  **Step 1:** What *is* the average salary?
2.  **Step 2:** Who earns more than that amount?

You can't do this in one simple `WHERE` clause (e.g., `WHERE Salary > AVG(Salary)` is not valid SQL). This is why we need a subquery.

#### The Sample Table (`employees`)

Let's use a simple table for this example.

```sql
-- Let's create a sample table for this lesson
CREATE TABLE employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(50),
    Department VARCHAR(50),
    Salary INT
);

INSERT INTO employees VALUES
(1, 'Rahul Verma', 'Tech', 80000),
(2, 'Priya Singh', 'HR', 65000),
(3, 'Amit Das', 'Tech', 95000),
(4, 'Sneha Baruah', 'Sales', 72000),
(5, 'Rajat Sharma', 'Sales', 60000);
```

#### Step 1: Solving the Inner Question

First, let's find the average salary.

```sql
SELECT AVG(Salary) FROM employees;
```

**Result of this inner query:**

```
+-------------+
| AVG(Salary) |
+-------------+
|    74400.00 |
+-------------+
```

The inner query gives us a single value: `74400`.

#### Step 2: Combining with a Subquery

Now, we embed that first query inside another query.

```sql
SELECT
    Name,
    Salary
FROM
    employees
WHERE
    Salary > (SELECT AVG(Salary) FROM employees);
```

### How the Database Executes This

This is the most important part to understand:

1.  **Run the Inner Query First:** The database completely ignores the outer query and runs the part in the parentheses: `(SELECT AVG(Salary) FROM employees)`.
2.  **Get the Result:** This query returns the single value `74400`.
3.  **Substitute the Result:** The database now substitutes that result into the outer query. The query you wrote effectively becomes:
    ```sql
    SELECT
        Name,
        Salary
    FROM
        employees
    WHERE
        Salary > 74400;
    ```
4.  **Run the Final Query:** Now this simple query runs, and you get your final answer.

#### The Final Output

```
+-------------+--------+
| Name        | Salary |
+-------------+--------+
| Rahul Verma |  80000 |
| Amit Das    |  95000 |
+-------------+--------+
```

This is called a **Scalar Subquery** because the inner query returns a single scalar value (one row, one column).

### The Next Step: Subqueries with `IN`

What if the inner query returns more than one row? For this, we use operators like `IN`.

Let's use our `train_info` and `stations` tables.

**The Goal:** Find the names of all trains that depart from any station in Bihar.

**The Two-Step Question:**

1.  **Step 1:** What are all the station codes for Bihar?
2.  **Step 2:** Which trains in `train_info` have a `SourceStation` that is in the list from Step 1?

#### The Subquery

```sql
SELECT TrainName, SourceStation
FROM train_info
WHERE SourceStation IN (SELECT StationCode FROM stations WHERE State = 'Bihar');
```

**How it works:**

1.  **Inner Query Runs:** `SELECT StationCode FROM stations WHERE State = 'Bihar'` runs first.
2.  **Get the Result:** It returns a list of values:
    ```
    'RJPB'
    'PNBE'
    'DBG'
    'GAYA'
    'MFP'
    ```
3.  **Substitute and Run Outer Query:** The outer query effectively becomes:
    ```sql
    SELECT TrainName, SourceStation
    FROM train_info
    WHERE SourceStation IN ('RJPB', 'PNBE', 'DBG', 'GAYA', 'MFP');
    ```
    The `IN` operator checks if the `SourceStation` matches **any** value in the list.

#### The Final Output

```
+------------------------+---------------+
| TrainName              | SourceStation |
+------------------------+---------------+
| Rajdhani Express       | RJPB          |
| Jan Shatabdi Express   | PNBE          |
| Bihar Sampark Kranti   | DBG           |
| Mahabodhi SF Express   | GAYA          |
| Pawan Express          | MFP           |
| Patna-CSMT Suvidha     | PNBE          |
| Gaya-Chennai Egmore SF | GAYA          |
| Patna-SMVB Humsafar    | PNBE          |
+------------------------+---------------+
```

This is the fundamental concept of subqueries. They let you perform sequential, logical steps within a single, powerful command. As you practice, you'll find they are an incredibly useful tool.