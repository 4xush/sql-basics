# Train Route Query Examples

This document provides examples of SQL queries on the `TrainRoutes` table to list existing reverse route pairs and remove reverse pairs using different methods.

---

## 1. Listing Existing Reverse Pairs

First, display all train routes:

```sql
SELECT *
FROM TrainRoutes;
```

**Example Output:**

```
+---------+-----------+----------+
| TrainNo | From_City | To_City  |
+---------+-----------+----------+
|   12023 | Kolkata   | Patna    |
|   12024 | Patna     | Kolkata  |
|   12389 | Gaya      | Chennai  |
|   12505 | Guwahati  | Delhi    |
|   12506 | Delhi     | Guwahati |
+---------+-----------+----------+
```

To list pairs of routes that are reverses of each other using a self join:

```sql
SELECT t1.*
FROM TrainRoutes t1
INNER JOIN TrainRoutes t2
  ON t2.To_City = t1.From_City;
```

**This returns:**

```
+---------+-----------+----------+
| TrainNo | From_City | To_City  |
+---------+-----------+----------+
|   12024 | Patna     | Kolkata  |
|   12023 | Kolkata   | Patna    |
|   12506 | Delhi     | Guwahati |
|   12505 | Guwahati  | Delhi    |
+---------+-----------+----------+
```

To avoid duplicate reverse pairs, add a filtering condition:

```sql
SELECT t1.*
FROM TrainRoutes t1
INNER JOIN TrainRoutes t2
  ON t2.To_City = t1.From_City
WHERE t1.TrainNo < t2.TrainNo;
```

This returns one row per reversible pair:

```
+---------+-----------+---------+
| TrainNo | From_City | To_City |
+---------+-----------+---------+
|   12023 | Kolkata   | Patna   |
|   12505 | Guwahati  | Delhi   |
+---------+-----------+---------+
```

---

## 2. Removing Reverse Pairs Using JOIN

### Explanation

The JOIN method uses a LEFT JOIN to identify reverse pairs. For example:

```sql
SELECT *
FROM TrainRoutes t1
LEFT JOIN TrainRoutes t2
  ON t2.To_City = t1.From_City;
```

This returns all routes alongside their reverse match (if available). To filter out duplicate reverse pairs and retain only one instance, use:

```sql
SELECT t1.*
FROM TrainRoutes t1
LEFT JOIN TrainRoutes t2
  ON t2.To_City = t1.From_City
WHERE t1.From_City < t1.To_City
   OR t2.From_City IS NULL;
```

**Example Output:**

```
+---------+-----------+----------+
| TrainNo | From_City | To_City  |
+---------+-----------+----------+
|   12023 | Kolkata   | Patna    |
|   12506 | Delhi     | Guwahati |
|   12389 | Gaya      | Chennai  |
+---------+-----------+----------+
```

---

## 3. Removing Reverse Pairs Using a Correlated Subquery


### 🔹 1. The `NOT EXISTS` logic

You wrote: WRONG QUERY - 

```sql
select * 
from TrainRoutes t1  
where not exists (
    select * 
    from TrainRoutes t2  
    where t1.from_city = t2.to_city 
      and t1.to_city   = t2.from_city 
      and t1.from_city > t2.to_city
);
```

This says:
*"Give me those routes where there is **no reverse route** in the table **with an extra condition** (`t1.from_city > t2.to_city`)."*

But because of the **extra condition** `t1.from_city > t2.to_city`, the reverse pairs are not being detected properly.
For example:

* (Kolkata → Patna) vs (Patna → Kolkata)

  * Here Kolkata is not greater than Patna alphabetically → so the condition fails → the reverse exists but your subquery doesn’t catch it.
  * So both rows still survive, even though they are actually reversible pairs.

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

This query excludes any route that has a reverse pair. If your intent is to remove one duplicate from each reverse pair, add a further condition to determine which row to keep. For example:

```sql
SELECT *
FROM TrainRoutes t1
WHERE NOT EXISTS (
    SELECT 1
    FROM TrainRoutes t2
    WHERE t1.From_City = t2.To_City
      AND t1.To_City   = t2.From_City
      AND t1.From_City > t2.From_City
);
```

In this query, the condition `t1.From_City > t2.From_City` ensures that only one route of the reversible pair is returned by discarding the duplicate.

here (Patna → Kolkata) vs (Kolkata → Patna)

 Here Patna is greater than Kolkata alphabetically → so the condition true → the reverse exists and now subquery does catch it.
  * So only one row will survive.

---
