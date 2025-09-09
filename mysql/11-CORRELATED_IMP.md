# SQL Correlated Subqueries for Ranking and Filtering

## Example 1: Fetching Top Three Salaries Using a Correlated Subquery

### Sample Data

First, let's examine our sample data:

```sql
SELECT w1.first_name, w1.salary FROM worker w1;
```

**Result:**

```
+------------+--------+
| first_name | salary |
+------------+--------+
| Amit       | 450000 |
| Amitabh    | 470000 |
| Arjun      | 200000 |
| Kiran      | 320000 |
| Suresh     | 470000 |
| Anita      | 400000 |
| Rohit      | 300000 |
| Priya      | 250000 |
| Vikram     | 250000 |
| Meena      | 500000 |
+------------+--------+
```

### Query for Top Three Salaries

To fetch workers with the top three salaries, we can use a correlated subquery with a counting mechanism:

```sql
SELECT w1.first_name, w1.salary
FROM worker w1
WHERE (
    SELECT COUNT(w2.salary)
    FROM worker w2
    WHERE w2.salary >= w1.salary
) <= 3
ORDER BY salary DESC;
```

**Result:**

```
+------------+--------+
| first_name | salary |
+------------+--------+
| Meena      | 500000 |
| Amitabh    | 470000 |
| Suresh     | 470000 |
+------------+--------+
```

### Explanation

This query works by:

1. For each row in the outer query (w1), the subquery counts how many salaries (including the current one) are greater than or equal to the current salary.
2. Only rows where this count is less than or equal to 3 are returned.
3. The results are ordered by salary in descending order.

This approach effectively ranks the salaries and returns only those with a rank of 3 or better.

## Example 2: Removing Reverse Pairs from Train Routes

### Incorrect Query Analysis

When trying to filter out one direction of each reversible route pair, a common mistake is using an incorrect condition:

```sql
SELECT *
FROM TrainRoutes t1
WHERE NOT EXISTS (
    SELECT *
    FROM TrainRoutes t2
    WHERE t1.from_city = t2.to_city
      AND t1.to_city = t2.from_city
      AND t1.from_city > t2.to_city
);
```

This says:
**\*Issue with this query:**
The condition `t1.from_city > t2.to_city` does not correctly identify reverse route pairs. For example, with routes (Kolkata → Patna) and (Patna → Kolkata):

- When evaluating the Kolkata → Patna row, the condition `Kolkata > Patna` is false alphabetically
- Therefore, this row is not identified as having a reverse pair and is incorrectly included in the results
- Both directions of the route survive in the output, despite being reversible pairs

---

### Correct Approaches

#### Option 1: Finding Routes Without Reverse Pairs

To exclude any route that has a reverse pair:

```sql
SELECT *
FROM TrainRoutes t1
WHERE NOT EXISTS (
    SELECT *
    FROM TrainRoutes t2
    WHERE t1.From_City = t2.To_City
      AND t1.To_City = t2.From_City
);
```

This query returns only routes that have no reverse direction in the table (one-way routes).

#### Option 2: Keeping One Direction of Each Reversible Pair\*

But because of the **extra condition** `t1.from_city > t2.to_city`, the reverse pairs are not being detected properly.
For example:

- (Kolkata → Patna) vs (Patna → Kolkata)

  - Here Kolkata is not greater than Patna alphabetically → so the condition fails → the reverse exists but your subquery doesn’t catch it.
  - So both rows still survive, even though they are actually reversible pairs.

---

CORRECT ONE -

### Explanation

The correlated subquery method leverages the `NOT EXISTS` clause to filter out reverse routes. Consider the following query:

```sql
SELECT *
FROM TrainRoutes t1
WHERE NOT EXISTS (
    SELECT *
    FROM TrainRoutes t2
    WHERE t1.From_City = t2.To_City
      AND t1.To_City   = t2.From_City
);
```

To retain only one direction of each reversible pair:

```sql
SELECT *
FROM TrainRoutes t1
WHERE NOT EXISTS (
    SELECT 1
    FROM TrainRoutes t2
    WHERE t1.From_City = t2.To_City
      AND t1.To_City = t2.From_City
      AND t1.From_City > t2.From_City
);
```

This query uses a lexicographic comparison (`t1.From_City > t2.From_City`) to consistently decide which direction to keep.

**Example:**
For routes (Patna → Kolkata) and (Kolkata → Patna):

- When evaluating Patna → Kolkata: Since "Patna" > "Kolkata" alphabetically, the condition is true, the subquery returns rows, and this route is excluded
- When evaluating Kolkata → Patna: Since "Kolkata" is not > "Patna", the condition is false, the subquery returns no rows, and this route is included

The result is that only one direction (Kolkata → Patna) remains in the output, ensuring a consistent approach to removing duplicates.
