# SELF JOIN in MySQL

A SELF JOIN is a join where a table is joined with itself. This is useful for comparing rows within the same table or finding relationships between rows in the same table.

Let's use the most classic and straightforward scenario for learning `SELF JOIN`: an **employee-manager relationship**.

### The Core Idea

The main idea is that we have a single table that contains two related types of information. In this case, our table has employees, and their managers... who are also employees listed in the same table.

A `SELF JOIN` is used to connect rows within this single table.

### 1\. The Simple `employees` Table

Here is a very basic table. The key is the `ManagerID` column, which refers back to another employee's `EmployeeID` in the same table.

**Table Schema (`CREATE TABLE`)**
This works for both MariaDB and SQLite.

```sql
CREATE TABLE employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    ManagerID INT  -- This column links to another EmployeeID in this same table
);
```

**Table Data (`INSERT`)**

```sql
INSERT INTO employees (EmployeeID, Name, ManagerID) VALUES
(1, 'Aarav Sharma', NULL),      -- The CEO has no manager
(2, 'Bhavna Kumar', 1),
(3, 'Chetan Desai', 1),
(4, 'Divya Reddy', 2),
(5, 'Farhan Ali', 2),
(6, 'Geeta Singh', 3);
```

### Your `employees` Table

After running the commands, your table looks like this. It's small and easy to understand.

| EmployeeID | Name         | ManagerID |
| :--------- | :----------- | :-------- |
| 1          | Aarav Sharma | NULL      |
| 2          | Bhavna Kumar | 1         |
| 3          | Chetan Desai | 1         |
| 4          | Divya Reddy  | 2         |
| 5          | Farhan Ali   | 2         |
| 6          | Geeta Singh  | 3         |

Look at the data:

- Bhavna Kumar's `ManagerID` is `1`. This points to Aarav Sharma's `EmployeeID`.
- Divya Reddy's `ManagerID` is `2`, which points to Bhavna Kumar.
- The CEO, Aarav Sharma, has a `NULL` `ManagerID` because he is at the top.

---

### The `SELF JOIN` in Action

**The Goal:** We want a list that shows each employee's name next to their manager's name.

If we just look at the table, we can see the `ManagerID`, but not the manager's _name_. To get the name, we must look up that ID in the very same table. This is where `SELF JOIN` is needed.

**The Logic:**

1.  We pretend we have two copies of the `employees` table.
2.  Let's call the first copy `E` (for **E**mployee).
3.  Let's call the second copy `M` (for **M**anager).
4.  We connect them where the employee's `ManagerID` (`E.ManagerID`) matches the manager's `EmployeeID` (`M.EmployeeID`).

**The SQL Query:**

```sql
SELECT
    E.Name AS EmployeeName,
    M.Name AS ManagerName
FROM
    employees AS E      -- The table treated as the "Employee"
JOIN
    employees AS M ON E.ManagerID = M.EmployeeID; -- The table treated as the "Manager"
```

**Result of the Query:**

| EmployeeName | ManagerName  |
| :----------- | :----------- |
| Bhavna Kumar | Aarav Sharma |
| Chetan Desai | Aarav Sharma |
| Divya Reddy  | Bhavna Kumar |
| Farhan Ali   | Bhavna Kumar |
| Geeta Singh  | Chetan Desai |

See how clear that is? The `SELF JOIN` successfully matched each employee with their manager's name from the same table.

### Bonus: What about the CEO?

You might notice Aarav Sharma isn't in the "EmployeeName" column. That's because he has a `NULL` `ManagerID`, and a standard `JOIN` (which is an `INNER JOIN`) only includes rows where the match is found.

If you want to list **all** employees, even those without a manager, you use a `LEFT JOIN`.

```sql
SELECT
    E.Name AS EmployeeName,
    M.Name AS ManagerName
FROM
    employees AS E
LEFT JOIN
    employees AS M ON E.ManagerID = M.EmployeeID;
```

**Result with `LEFT JOIN`:**

| EmployeeName | ManagerName  |
| :----------- | :----------- |
| Aarav Sharma | NULL         |
| Bhavna Kumar | Aarav Sharma |
| Chetan Desai | Aarav Sharma |
| Divya Reddy  | Bhavna Kumar |
| Farhan Ali   | Bhavna Kumar |
| Geeta Singh  | Chetan Desai |

This simple employee-manager table is the best way to understand the core logic of a `SELF JOIN`.

### MISTAKE :

```sql
MariaDB [punisher]> select * from employees e left join employees m on e.employeeid = m.managerid;
```

You've discovered that changing the join condition, even slightly, completely changes the question you're asking the database.

Let's break down exactly what your query is doing, step-by-step.

### The Question Your Query is Asking

First, let's translate your SQL into plain English. The previous query we looked at was: `ON E.ManagerID = M.EmployeeID` which means "Match an employee to their manager."

Your new query is: `ON e.employeeid = m.managerid`

The key difference is the `ON` clause:

- **To find an employee's manager:** `ON E.ManagerID = M.EmployeeID`
  - (Match the employee's `ManagerID` column to the manager's `EmployeeID` column)
- **To find an employee's direct reports (your query):** `ON E.EmployeeID = M.ManagerID`
  - (Match the manager's `EmployeeID` column to the employee's `ManagerID` column)

## CORRECT WAY :

```sql
SELECT
    E.Name AS EmployeeName,
    M.Name AS ManagerName
FROM
    employees AS E
LEFT JOIN
    employees AS M ON E.ManagerID = M.EmployeeID;
```

**This will search -> Which employee M is the manager of this employee E seaching in the same table M.**

The goal of this query is to list **every single employee** and show their manager's name next to them. The `LEFT JOIN` is crucial because it ensures even employees without a manager (like the CEO) are included in the final list.

### Step 1: Visualizing the Two Tables

First, let's imagine the database creates two virtual copies of your `employees` table. We'll call them `E` (for the Employee) and `M` (for the Manager), just like in the query.

**Table 1: `employees AS E` (The Employee Side)**
This is our main list. We will go through this table one row at a time.

```
+------------+--------------+-----------+
| EmployeeID | Name         | ManagerID |
+------------+--------------+-----------+
|          1 | Aarav Sharma |      NULL |
|          2 | Bhavna Kumar |         1 |
|          3 | Chetan Desai |         1 |
|          4 | Divya Reddy  |         2 |
|          5 | Farhan Ali   |         2 |
|          6 | Geeta Singh  |         3 |
+------------+--------------+-----------+
```

**Table 2: `employees AS M` (The Manager Side)**
This table is used as a "lookup" to find the manager's name.

```
+------------+--------------+-----------+
| EmployeeID | Name         | ManagerID |
+------------+--------------+-----------+
|          1 | Aarav Sharma |      NULL |
|          2 | Bhavna Kumar |         1 |
|          3 | Chetan Desai |         1 |
|          4 | Divya Reddy  |         2 |
|          5 | Farhan Ali   |         2 |
|          6 | Geeta Singh  |         3 |
+------------+--------------+-----------+
```

### Step 2: The Row-by-Row Comparison

The database will now walk through each row of the **left table (`E`)** and try to find a match in the **right table (`M`)** based on the rule `ON E.ManagerID = M.EmployeeID`.

---

#### **Comparison \#1: Aarav Sharma (The CEO)**

1.  **Take the first row from `E`:**
    `1 | Aarav Sharma | NULL`

2.  **Look at the join condition:** `E.ManagerID` is `NULL`. The query tries to find a row in table `M` where `M.EmployeeID = NULL`.

3.  **Find a match:** In SQL, `NULL` never equals `NULL`. So, **no match is found**.

4.  **`LEFT JOIN` Rule:** The rule says: "Even if there's no match, you **must** keep the row from the left table (`E`). For the columns from the right table (`M`), just put `NULL`."

5.  **Resulting Row:**

    - `E.Name` is 'Aarav Sharma'.
    - `M.Name` is `NULL` (because no match was found).
    - **Output Row:** `Aarav Sharma | NULL`

---

#### **Comparison \#2: Bhavna Kumar**

1.  **Take the second row from `E`:**
    `2 | Bhavna Kumar | 1`

2.  **Look at the join condition:** `E.ManagerID` is `1`. The query now looks for a row in table `M` where `M.EmployeeID = 1`.

3.  **Find a match:** **Success\!** It finds a match in table `M`:
    `1 | Aarav Sharma | NULL`

4.  **Combine the results:** The query now takes the columns it needs from both the row in `E` and the matching row in `M`.

5.  **Resulting Row:**

    - `E.Name` is 'Bhavna Kumar'.
    - `M.Name` from the matched row is 'Aarav Sharma'.
    - **Output Row:** `Bhavna Kumar | Aarav Sharma`

---

### Step 3: The Final Output

The database does this for every row in the left table `E`. When it's all done, the final result is exactly what we built step-by-step:

```
+--------------+----------------+
| EmployeeName | ManagerName    |
+--------------+----------------+
| Aarav Sharma | NULL           |
| Bhavna Kumar | Aarav Sharma   |
| Chetan Desai | Aarav Sharma   |
| Divya Reddy  | Bhavna Kumar   |
| Farhan Ali   | Bhavna Kumar   |
| Geeta Singh  | Chetan Desai   |
+--------------+----------------+
```

## Final Example Query

Here's the correct SELF JOIN query to list all employees and their managers:

```sql
SELECT
    e.name AS EmployeeName,
    m.name AS ManagerName
FROM
    employees e
LEFT JOIN
    employees m ON e.managerid = m.employeeid;
```

### Explanation

This is a SELF JOIN where:

- `e` represents the employee table (alias for employees).
- `m` represents the manager table (another alias for the same employees table).
- The join condition `e.managerid = m.employeeid` matches each employee's manager ID to the manager's employee ID in the same table.

For each employee, it finds their manager's name from the same table.

### Result

```
+--------------+----------------+
| EmployeeName | ManagerName    |
+--------------+----------------+
| Aarav Sharma | NULL           |
| Bhavna Kumar | Aarav Sharma   |
| Chetan Desai | Aarav Sharma   |
| Divya Reddy  | Bhavna Kumar   |
| Farhan Ali   | Bhavna Kumar   |
| Geeta Singh  | Chetan Desai   |
+--------------+----------------+
```

This demonstrates how SELF JOIN can be used to find hierarchical relationships within a single table.

## INNER JOIN vs LEFT JOIN in SELF JOIN

When using SELF JOIN, the choice between INNER JOIN and LEFT JOIN affects which rows are included in the result.

### INNER JOIN Issue: Missing Employees Without Managers

If you use INNER JOIN instead of LEFT JOIN, employees without managers (like the CEO) will be excluded from the results.

**Query with INNER JOIN:**

```sql
SELECT
    e.name AS EmployeeName,
    ee.name AS ManagerName
FROM
    employees e
INNER JOIN
    employees ee ON e.managerid = ee.employeeid;
```

**Result:**

```
+--------------+--------------+
| EmployeeName | ManagerName  |
+--------------+--------------+
| Bhavna Kumar | Aarav Sharma |
| Chetan Desai | Aarav Sharma |
| Divya Reddy  | Bhavna Kumar |
| Farhan Ali   | Bhavna Kumar |
| Geeta Singh  | Chetan Desai |
+--------------+--------------+
```

**Why is Aarav Sharma missing?**

- Aarav Sharma has `ManagerID = NULL`
- INNER JOIN only includes rows where there is a match in both tables
- Since `NULL` doesn't match any `EmployeeID`, Aarav Sharma is excluded

### Solution: Use LEFT JOIN

To include all employees, including those without managers, use LEFT JOIN:

```sql
SELECT
    e.name AS EmployeeName,
    ee.name AS ManagerName
FROM
    employees e
LEFT JOIN
    employees ee ON e.managerid = ee.employeeid;
```

This ensures all employees are listed, with `NULL` for those without managers.


