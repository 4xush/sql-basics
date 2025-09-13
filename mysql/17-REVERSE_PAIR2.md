# Alternative Methods for Handling Reverse Pairs in SQL

This document explains additional techniques for handling reverse route pairs, building on the methods discussed in [16-REVERSE_PAIR.md](./16-REVERSE_PAIR.md).

## Method 4: Using LEAST and GREATEST Functions

The `LEAST` and `GREATEST` functions provide a clever way to standardize the representation of route pairs, effectively collapsing reverse pairs into a single row.

```sql
SELECT DISTINCT
    LEAST(from_city, to_city) AS city1,
    GREATEST(from_city, to_city) AS city2
FROM TrainRoutes;
```

### Example Output:

```
+----------+----------+
| city1    | city2    |
+----------+----------+
| Delhi    | Guwahati |
| Chennai  | Gaya     |
| Kolkata  | Patna    |
+----------+----------+
```

### How It Works:

1. For each row, `LEAST(from_city, to_city)` returns the city name that comes first alphabetically
2. `GREATEST(from_city, to_city)` returns the city name that comes later alphabetically
3. This creates a standardized representation regardless of direction:
   - For "Kolkata → Patna", it returns (Kolkata, Patna)
   - For "Patna → Kolkata", it also returns (Kolkata, Patna)
4. The `DISTINCT` keyword eliminates duplicates

### Advantages:

- Very concise solution
- Handles all reverse pairs automatically
- No need for joins or complex subqueries

### Limitations:

- Only returns the city pairs, not the original train numbers or complete route details
- May not be suitable when you need to preserve specific attributes of the original routes
- Does not distinguish between one-way and two-way routes

## Additional Methods for Handling Reverse Pairs

### Method 5: Using Window Functions

If your SQL implementation supports window functions (MySQL 8.0+, PostgreSQL, SQL Server, etc.), you can use them to identify and handle reverse pairs:

```sql
WITH RankedRoutes AS (
    SELECT
        t.*,
        ROW_NUMBER() OVER (
            PARTITION BY
                LEAST(from_city, to_city),
                GREATEST(from_city, to_city)
            ORDER BY TrainNo
        ) AS rn
    FROM TrainRoutes t
)
SELECT TrainNo, From_City, To_City
FROM RankedRoutes
WHERE rn = 1;
```

This approach:

1. Creates a common table expression (CTE) with routes grouped by standardized city pairs
2. Assigns a row number to each route within its city pair group
3. Selects only the first route for each city pair (lowest train number)

### Method 6: Using GROUP BY with Aggregation

This method retrieves one train for each city pair:

```sql
SELECT
    MIN(TrainNo) AS TrainNo,
    CASE
        WHEN MIN(CONCAT(From_City, To_City)) = CONCAT(From_City, To_City) THEN From_City
        ELSE To_City
    END AS From_City,
    CASE
        WHEN MIN(CONCAT(From_City, To_City)) = CONCAT(From_City, To_City) THEN To_City
        ELSE From_City
    END AS To_City
FROM TrainRoutes
GROUP BY LEAST(From_City, To_City), GREATEST(From_City, To_City);
```

This solution:

1. Groups routes by standardized city pairs
2. Selects the route with the lowest train number
3. Preserves the original direction of that route

## Recommendations for Handling Reverse Pairs

When deciding how to handle reverse pairs, consider:

1. **What data you need to preserve:**

   - If you only need the city pairs: Method 4 (LEAST/GREATEST)
   - If you need complete route details: Methods 3, 5, or 6

2. **Performance considerations:**

   - For large datasets, methods without joins (4, 5, 6) may perform better
   - For small datasets, any method is generally fine

3. **Readability and maintainability:**

   - The LEAST/GREATEST approach (Method 4) is most concise and easiest to understand
   - The correlated subquery (Method 3) is explicit about which routes to keep

4. **Database compatibility:**
   - Method 5 requires window function support (MySQL 8.0+, PostgreSQL, SQL Server)
   - Methods 1-4 and 6 work on most SQL implementations

For the most versatile solution that preserves all route information, Method 5 (window functions) is recommended when available, followed by Method 3 (correlated subquery).
