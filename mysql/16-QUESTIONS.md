# Correlated Subquery Practice Questions: Basic to Advanced

This document contains a collection of correlated subquery questions of increasing difficulty. Each question includes table creation statements and sample data to help you practice.

## Setting Up the Tables

First, let's create the tables we'll use for our questions. Execute these commands to set up your practice environment:

```sql
-- Create Employees table
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    salary DECIMAL(10, 2),
    department_id INT,
    manager_id INT,
    hire_date DATE
);

-- Create Departments table
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50),
    location VARCHAR(50)
);

-- Create Orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10, 2)
);

-- Create Order_Items table
CREATE TABLE order_items (
    item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    unit_price DECIMAL(10, 2)
);

-- Create Products table
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(50),
    price DECIMAL(10, 2)
);

-- Create Customers table
CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    city VARCHAR(50),
    state VARCHAR(50),
    country VARCHAR(50),
    postal_code VARCHAR(20)
);
```

## Sample Data Insertion

Populate the tables with sample data:

```sql
-- Insert data into Employees
INSERT INTO employees VALUES
(1, 'John', 'Doe', 75000.00, 1, NULL, '2015-05-15'),
(2, 'Jane', 'Smith', 65000.00, 1, 1, '2016-07-12'),
(3, 'Alice', 'Johnson', 80000.00, 2, 1, '2015-01-10'),
(4, 'Bob', 'Brown', 70000.00, 2, 3, '2017-03-20'),
(5, 'Charlie', 'Davis', 85000.00, 3, 1, '2014-11-05'),
(6, 'Diana', 'Wilson', 60000.00, 3, 5, '2019-08-15'),
(7, 'Edward', 'Taylor', 55000.00, 4, 1, '2020-01-10'),
(8, 'Fiona', 'Miller', 62000.00, 4, 7, '2021-04-30'),
(9, 'George', 'Clark', 72000.00, 1, 1, '2018-09-22'),
(10, 'Hannah', 'White', 90000.00, 2, 3, '2016-12-15');

-- Insert data into Departments
INSERT INTO departments VALUES
(1, 'HR', 'New York'),
(2, 'Engineering', 'San Francisco'),
(3, 'Finance', 'Chicago'),
(4, 'Marketing', 'Los Angeles');

-- Insert data into Customers
INSERT INTO customers VALUES
(1, 'ABC Corp', 'New York', 'NY', 'USA', '10001'),
(2, 'XYZ Inc', 'San Francisco', 'CA', 'USA', '94103'),
(3, 'Acme Ltd', 'Chicago', 'IL', 'USA', '60601'),
(4, 'Global Co', 'Toronto', 'ON', 'Canada', 'M5V 2L7'),
(5, 'Local Shop', 'London', NULL, 'UK', 'EC1A 1BB');

-- Insert data into Products
INSERT INTO products VALUES
(1, 'Laptop', 'Electronics', 1200.00),
(2, 'Smartphone', 'Electronics', 800.00),
(3, 'Desk Chair', 'Furniture', 250.00),
(4, 'Coffee Maker', 'Appliances', 120.00),
(5, 'Headphones', 'Electronics', 150.00);

-- Insert data into Orders
INSERT INTO orders VALUES
(101, 1, '2023-01-15', 2400.00),
(102, 2, '2023-01-20', 950.00),
(103, 3, '2023-02-05', 1350.00),
(104, 1, '2023-02-10', 800.00),
(105, 4, '2023-03-01', 1500.00),
(106, 2, '2023-03-15', 250.00),
(107, 5, '2023-03-20', 1200.00),
(108, 3, '2023-04-01', 920.00),
(109, 1, '2023-04-10', 400.00),
(110, 4, '2023-04-15', 2100.00);

-- Insert data into Order_Items
INSERT INTO order_items VALUES
(1001, 101, 1, 2, 1200.00),
(1002, 102, 2, 1, 800.00),
(1003, 102, 3, 1, 150.00),
(1004, 103, 1, 1, 1200.00),
(1005, 103, 5, 1, 150.00),
(1006, 104, 2, 1, 800.00),
(1007, 105, 1, 1, 1200.00),
(1008, 105, 5, 2, 150.00),
(1009, 106, 3, 1, 250.00),
(1010, 107, 1, 1, 1200.00),
(1011, 108, 2, 1, 800.00),
(1012, 108, 4, 1, 120.00),
(1013, 109, 4, 1, 120.00),
(1014, 109, 5, 1, 150.00),
(1015, 110, 1, 1, 1200.00),
(1016, 110, 2, 1, 800.00),
(1017, 110, 4, 1, 100.00);
```

## Basic Correlated Subquery Questions

### Question 1: Employees earning above average salary in their department

Find all employees who earn more than the average salary in their respective departments.

```sql
SELECT e.emp_id, e.first_name, e.last_name, e.salary, e.department_id
FROM employees e
WHERE e.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
);
```

**Explanation:**

- For each employee in the outer query, the correlated subquery calculates the average salary for their department
- The outer query only keeps employees whose salary is higher than their department's average
- The correlation is established through `e2.department_id = e.department_id`

### Question 2: Products that are more expensive than average product in their category

Find all products that have a price higher than the average price of products in the same category.

```sql
SELECT p.product_id, p.product_name, p.category, p.price
FROM products p
WHERE p.price > (
    SELECT AVG(p2.price)
    FROM products p2
    WHERE p2.category = p.category
);
```

**Explanation:**

- For each product in the outer query, the correlated subquery calculates the average price for its category
- The outer query filters for products priced higher than their category average
- The correlation is made through `p2.category = p.category`

### Question 3: Customers with orders above their personal average

Find customers who have placed an order with a total amount exceeding their personal average order amount.

```sql
SELECT c.customer_id, c.customer_name, o.order_id, o.total_amount
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE o.total_amount > (
    SELECT AVG(o2.total_amount)
    FROM orders o2
    WHERE o2.customer_id = c.customer_id
);
```

**Explanation:**

- The query joins customers and orders to get customer details with their orders
- For each customer-order pair, the correlated subquery calculates that customer's average order amount
- Only orders above the customer's personal average are returned

## Intermediate Correlated Subquery Questions

### Question 4: Find employees who earn more than their manager

Identify all employees who have a higher salary than their direct manager.

```sql
SELECT e.emp_id, e.first_name, e.last_name, e.salary
FROM employees e
WHERE e.salary > (
    SELECT m.salary
    FROM employees m
    WHERE m.emp_id = e.manager_id
);
```

**Explanation:**

- For each employee in the outer query, the correlated subquery fetches their manager's salary
- The outer query filters for employees whose salary exceeds their manager's salary
- The correlation is established through `m.emp_id = e.manager_id`

### Question 5: Departments with higher than average salaries across the company

Find departments where the average salary is higher than the company-wide average salary.

```sql
SELECT d.dept_id, d.dept_name, AVG(e.salary) AS avg_dept_salary
FROM departments d
JOIN employees e ON d.dept_id = e.department_id
GROUP BY d.dept_id, d.dept_name
HAVING AVG(e.salary) > (
    SELECT AVG(salary)
    FROM employees
);
```

**Explanation:**

- The outer query groups employees by department and calculates each department's average salary
- The correlated subquery calculates the company-wide average salary
- The HAVING clause filters for departments with an average salary above the company average

### Question 6: Customers who have ordered all products in a category

Find customers who have ordered every product in the 'Electronics' category.

```sql
SELECT c.customer_id, c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT p.product_id
    FROM products p
    WHERE p.category = 'Electronics'
    AND NOT EXISTS (
        SELECT 1
        FROM orders o
        JOIN order_items oi ON o.order_id = oi.order_id
        WHERE o.customer_id = c.customer_id
        AND oi.product_id = p.product_id
    )
);
```

**Explanation:**

- For each customer, check if there exists any 'Electronics' product that they haven't ordered
- If no such product exists (NOT EXISTS), then the customer has ordered all electronics products
- This uses a nested correlated subquery with correlations to both the customer and product tables

## Advanced Correlated Subquery Questions

### Question 7: Rank employees by salary within each department

Rank employees by their salary within each department without using window functions.

```sql
SELECT e1.emp_id, e1.first_name, e1.last_name, e1.department_id, e1.salary,
    (SELECT COUNT(*) + 1
     FROM employees e2
     WHERE e2.department_id = e1.department_id
     AND e2.salary > e1.salary) AS salary_rank
FROM employees e1
ORDER BY e1.department_id, salary_rank;
```

**Explanation:**

- For each employee, the correlated subquery counts how many employees in the same department have higher salaries
- Adding 1 to this count gives the salary rank (1 = highest salary)
- The result is ordered by department and rank for clear viewing

### Question 8: Find employees with the top N salaries in each department

Find the top 3 highest-paid employees in each department without using window functions.

```sql
SELECT e1.emp_id, e1.first_name, e1.last_name, e1.department_id, e1.salary
FROM employees e1
WHERE (
    SELECT COUNT(*)
    FROM employees e2
    WHERE e2.department_id = e1.department_id
    AND e2.salary >= e1.salary
) <= 3
ORDER BY e1.department_id, e1.salary DESC;
```

**Explanation:**

- For each employee, the correlated subquery counts how many employees in the same department have an equal or higher salary
- If this count is ≤ 3, the employee is among the top 3 highest-paid in their department
- The result is ordered by department and salary for clear viewing

### Question 9: Customers whose all orders exceed a certain amount

Find customers for whom all orders have a total amount greater than $1000.

```sql
SELECT c.customer_id, c.customer_name
FROM customers c
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.customer_id = c.customer_id
    AND o.total_amount <= 1000
);
```

**Explanation:**

- For each customer, the correlated subquery checks if there exists any order with amount ≤ $1000
- If no such order exists (NOT EXISTS), all the customer's orders exceed $1000
- The correlation is established through `o.customer_id = c.customer_id`

### Question 10: The most expensive product ordered by each customer

For each customer, find the most expensive product they have ever ordered.

```sql
SELECT c.customer_id, c.customer_name, p.product_id, p.product_name, p.price
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
WHERE p.price = (
    SELECT MAX(p2.price)
    FROM orders o2
    JOIN order_items oi2 ON o2.order_id = oi2.order_id
    JOIN products p2 ON oi2.product_id = p2.product_id
    WHERE o2.customer_id = c.customer_id
);
```

**Explanation:**

- The outer query joins customers, orders, order items, and products to get full information
- The correlated subquery finds the maximum price of any product ordered by each customer
- The WHERE clause filters to keep only the customer-product pairs where the product is the most expensive one ordered by that customer
- The correlation is made through `o2.customer_id = c.customer_id`

## Conclusion

Correlated subqueries are powerful tools in SQL for solving complex problems that involve comparing data within groups or across related tables. The examples progress from basic comparisons to complex ranking and filtering operations, demonstrating the versatility of this technique.

When working with correlated subqueries, remember:

1. They execute for each row of the outer query, which can impact performance with large datasets
2. They often can be rewritten using joins, window functions, or CTEs for better performance
3. Proper indexing on the correlation columns is essential for good performance
