# Common SQL Mistakes

## Mistake 1: Incorrect `ORDER BY` Syntax

A common mistake is forgetting to specify a column name when using `ORDER BY`.

**Incorrect Example:**

```sql
SELECT DISTINCT salary FROM worker ORDER BY DESC;
```

**Explanation:**

This query is incorrect because it does not specify the column on which to sort. The `ORDER BY` clause requires the column name followed by the sort direction (`ASC` or `DESC`).

**Correct Query:**

```sql
SELECT DISTINCT salary FROM worker ORDER BY salary DESC;
```

---

## Mistake 2: Errors in Correlated Subquery for Ranking

When writing a correlated subquery to fetch the top 3 salaries, an error may occur if the ranking condition is not set properly.

**Mistaken Query:**

```sql
WHERE 3 <= (
    SELECT COUNT(w2.worker_id)
    FROM worker w2
    WHERE w2.salary >= w1.salary
);
```

**Issue:**

The aim is to retrieve rows corresponding to the top 3 salaries, but the above condition might return rows where the count equals 3, which may not be desired depending on your ranking logic. The intention is to filter for rows where the count of salaries greater than or equal to the current row is less than or equal to 3.

**Correct Query Option 1:**

```sql
WHERE (
    SELECT COUNT(w2.worker_id)
    FROM worker w2
    WHERE w2.salary >= w1.salary
) <= 3;
```

**Correct Query Option 2:**

```sql
WHERE 3 >= (
    SELECT COUNT(w2.worker_id)
    FROM worker w2
    WHERE w2.salary >= w1.salary
);
```

_Note:_ Both corrected queries ensure that only rows with rank 3 (or higher) are returned. Choose the option that best fits your logical requirements.

---

## Mistake 3: Incorrect Grouping in SQL Queries

A common mistake when working with the `GROUP BY` clause is misunderstanding how grouping operates. Remember that the `GROUP BY` clause aggregates rows based on the specified columns, and then aggregate functions (such as `SUM()`, `COUNT()`, `AVG()`, etc.) are applied to each group.

**Key Points:**

- **Grouping Behavior:** The `GROUP BY` clause groups all the rows that have the same values in the specified columns. After grouping, aggregate functions are applied to each group, resulting in one row per group (unless more than one row is expected because of multiple groups).
- **Common Pitfall:** Selecting columns that are not included in the `GROUP BY` clause without using an aggregate function can cause logical errors or lead to unexpected results. In strict SQL modes, it might even throw an error.

**Incorrect Example:**

```sql
SELECT worker_id, salary
FROM worker
GROUP BY salary;
```

_Issue:_ Here, only `salary` is used for grouping. The `worker_id` is not aggregated or included in the `GROUP BY` clause, which can lead to ambiguous results or errors.

**Correct Approach:**

You should either include all non-aggregated columns in the `GROUP BY` clause or apply an aggregate function to the columns that aren’t grouped.

**Option 1: Include All Non-Aggregated Columns**

```sql
SELECT worker_id, salary
FROM worker
GROUP BY worker_id, salary;
```

**Option 2: Use an Aggregate Function**

If your intention is to aggregate data, for example, to retrieve the maximum salary for each department, do it as follows:

```sql
SELECT department, MAX(salary) AS max_salary
FROM worker
GROUP BY department;
```

This way, each group (department) returns a single row with the aggregated value.

_Note:_ Always verify that the columns in your `SELECT` statement align with the grouping logic to avoid unexpected results.
