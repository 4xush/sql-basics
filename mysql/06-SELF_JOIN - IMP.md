## SELF JOIN - IMP

### 1\. The Sample `TrainRoutes` Table

Let's create a simple table with just the information we need. I've included a Guwahati-Delhi round trip for you.

**Table Schema (`CREATE TABLE`)**

```sql
CREATE TABLE TrainRoutes (
    TrainNo INT PRIMARY KEY,
    From_City VARCHAR(50),
    To_City VARCHAR(50)
);
```

**Table Data (`INSERT`)**

```sql
INSERT INTO TrainRoutes (TrainNo, From_City, To_City) VALUES
(12505, 'Guwahati', 'Delhi'),
(12506, 'Delhi', 'Guwahati'),
(12024, 'Patna', 'Kolkata'),
(12023, 'Kolkata', 'Patna'),
(12389, 'Gaya', 'Chennai');
```

**Your `TrainRoutes` Table:**

| TrainNo | From_City | To_City  |
| :------ | :-------- | :------- |
| 12505   | Guwahati  | Delhi    |
| 12506   | Delhi     | Guwahati |
| 12024   | Patna     | Kolkata  |
| 12023   | Kolkata   | Patna    |
| 12389   | Gaya      | Chennai  |

As you can see, we have two pairs of "round trip" trains and one train (Gaya to Chennai) that doesn't have a return trip in this table.

### 2\. The `SELF JOIN` Query

Here is the query to find these pairs.

**To find pairs of trains that travel in opposite directions between the same two cities, effectively identifying 'round trip' routes within your `TrainRoutes` table.**

```sql
SELECT
    T1.TrainNo AS OutboundTrain,
    T2.TrainNo AS ReturnTrain,
    T1.From_City AS City_A,
    T1.To_City AS City_B
FROM
    TrainRoutes AS T1
JOIN
    TrainRoutes AS T2 ON T1.From_City = T2.To_City
                      AND T1.To_City = T2.From_City
WHERE
    T1.TrainNo < T2.TrainNo;
```

### 3\. How It Happens: Step-by-Step Comparison

Let's visualize the two tables and see how the database finds the matches.

**Table 1: `TrainRoutes AS T1` (The Outbound Trip)**

| TrainNo | From_City | To_City  |
| :------ | :-------- | :------- |
| 12505   | Guwahati  | Delhi    |
| 12506   | Delhi     | Guwahati |
| 12024   | Patna     | Kolkata  |
| 12023   | Kolkata   | Patna    |
| 12389   | Gaya      | Chennai  |

**Table 2: `TrainRoutes AS T2` (The Return Trip)**

| TrainNo | From_City | To_City  |
| :------ | :-------- | :------- |
| 12505   | Guwahati  | Delhi    |
| 12506   | Delhi     | Guwahati |
| 12024   | Patna     | Kolkata  |
| 12023   | Kolkata   | Patna    |
| 12389   | Gaya      | Chennai  |

---

#### **Comparison \#1: The Guwahati-Delhi Pair**

1.  **Take the first row from `T1`:** `12505 | Guwahati | Delhi`

2.  **Look at the join condition:**

    - `T1.From_City` is 'Guwahati'
    - `T1.To_City` is 'Delhi'

3.  **Find a match:** The database now searches all of `T2` for a row that meets **both** of these conditions:

    - `T2.To_City` must be 'Guwahati'
    - `T2.From_City` must be 'Delhi'
      It finds a perfect match: the row `12506 | Delhi | Guwahati` in `T2`.

4.  **Check the `WHERE` clause:** The query now checks the condition `WHERE T1.TrainNo < T2.TrainNo`.

    - `T1.TrainNo` is `12505`.
    - `T2.TrainNo` is `12506`.
    - Is `12505 < 12506`? **Yes, it is TRUE.**

5.  **Resulting Row:** Because both the `ON` and `WHERE` conditions are met, a row is added to the output.

    - **Output Row:** `12505 | 12506 | Guwahati | Delhi`

---

#### **Comparison \#2: The Patna-Kolkata Pair**

This works exactly the same way.

1.  **Take the third row from `T1`:** `12024 | Patna | Kolkata`
2.  **Find a match in `T2`:** It searches for a train where `To_City` is 'Patna' and `From_City` is 'Kolkata'. It finds the row `12023 | Kolkata | Patna`.
3.  **Check the `WHERE` clause:** Is `12024 < 12023`? **No, it is FALSE.**
4.  **Resulting Row:** This pair is **discarded** for now.

Wait, why was it discarded? This brings us to the most important part of this query...

### The Magic of `WHERE T1.TrainNo < T2.TrainNo`

This line is crucial to avoid getting duplicate pairs. Let's see what happens when the database gets to the row for train 12023 in `T1`.

#### **Comparison \#3: The Kolkata-Patna Pair (The "other half")**

1.  **Take the fourth row from `T1`:** `12023 | Kolkata | Patna`
2.  **Find a match in `T2`:** It searches for a train where `To_City` is 'Kolkata' and `From_City` is 'Patna'. It finds the row `12024 | Patna | Kolkata`.
3.  **Check the `WHERE` clause:** Is `12023 < 12024`? **Yes, it is TRUE.**
4.  **Resulting Row:** This pair is **kept**\!
    - **Output Row:** `12023 | 12024 | Kolkata | Patna`

Without the `WHERE` clause, you would get both `(12024, 12023)` and `(12023, 12024)` in your results. This simple condition ensures each pair is listed only once.

### Final Output

After checking all combinations, the final output of the query is:

| OutboundTrain | ReturnTrain | City_A   | City_B |
| :------------ | :---------- | :------- | :----- |
| 12505         | 12506       | Guwahati | Delhi  |
| 12023         | 12024       | Kolkata  | Patna  |

### Summary: Putting It All Together

Let's trace the Guwahati-Delhi pair through the entire process:

1.  **`FROM/JOIN`**: The database considers the pair where `T1` is row `12505` and `T2` is row `12506`.
2.  **`ON` Check**:
    - Is `T1.From_City` ('Guwahati') equal to `T2.To_City` ('Guwahati')? **Yes.**
    - Is `T1.To_City` ('Delhi') equal to `T2.From_City` ('Delhi')? **Yes.**
    - The `ON` condition is met. This is a valid pair.
3.  **`WHERE` Check**:
    - Is `T1.TrainNo` (12505) less than `T2.TrainNo` (12506)? **Yes.**
    - The `WHERE` condition is met. The pair is kept.
4.  **`SELECT`**: The query now constructs the output row: `12505 | 12506 | Guwahati | Delhi`.

## The Problem: Redundant Pairs

As your output shows, your current query is logically correct in finding the pairs, but it's inefficient because it gives you both `Patna -> Kolkata` and `Kolkata -> Patna` as separate results.

```
+-----------+----------+
| from_city | to_city  |
+-----------+----------+
| Patna     | Kolkata  |
| Kolkata   | Patna    |
| Delhi     | Guwahati |
| Guwahati  | Delhi    |
+-----------+----------+
```

We want to filter this result set down so that each pair of cities appears only once. Here are the two complete and reliable ways to do it.

---

### Solution 1: Comparing by City Name (The Logical Method)

This method establishes a consistent order by comparing the city names alphabetically. The rule is simple: we only select the row where the starting city's name comes before the destination city's name alphabetically.

#### The Full Query

```sql
SELECT
    t1.From_City AS City_A,
    t1.To_City AS City_B
FROM
    TrainRoutes AS t1
JOIN
    TrainRoutes AS t2 ON t1.From_City = t2.To_City
                      AND t1.To_City = t2.From_City
WHERE
    t1.From_City < t2.From_City;
```

#### The Output (Correct and De-duplicated)

```
+---------+----------+
| City_A  | City_B   |
+---------+----------+
| Delhi   | Guwahati |
| Kolkata | Patna    |
+---------+----------+
```

**How it works:**

- For the Delhi-Guwahati pair, the query finds both `Delhi -> Guwahati` and `Guwahati -> Delhi`.
- The `WHERE` clause checks:
  - Is `'Delhi' < 'Guwahati'`? **TRUE**. This row is kept.
  - Is `'Guwahati' < 'Delhi'`? **FALSE**. This row is discarded.
- The result is one clean entry for the pair.

---

### Solution 2: Comparing by Train Number (The Standard Method)

This method uses the unique `TrainNo` (the Primary Key) to break the tie. The rule is arbitrary but consistent: we only select the pair where the `TrainNo` of the first train (`t1`) is smaller than the `TrainNo` of the second train (`t2`).

#### The Full Query

```sql
SELECT
    t1.From_City,
    t1.To_City
FROM
    TrainRoutes AS t1
JOIN
    TrainRoutes AS t2 ON t1.From_City = t2.To_City
                      AND t1.To_City = t2.From_City
WHERE
    t1.TrainNo < t2.TrainNo;
```

#### The Output (Correct and De-duplicated)

```
+-----------+----------+
| from_city | to_city  |
+-----------+----------+
| Guwahati  | Delhi    |
| Kolkata   | Patna    |
+-----------+----------+
```

_(Note: The city order in the output might be different from Solution 1, but each pair of cities still appears only once. The result is equally correct.)_

---

### Will It Always Work? A Detailed Comparison

This is the most important question. Yes, for all practical purposes, both methods will always work reliably. Here’s the breakdown:

#### Method 1: Comparing City Names (`WHERE t1.From_City < t2.From_City`)

- **Reliability:** **Extremely High.** As long as your city names are consistent (e.g., you don't have 'Delhi' in one row and 'delhi ' with a space in another), the alphabetical comparison is a deterministic and reliable way to pick exactly one of the two pairs. MariaDB's default text comparison is case-insensitive, which helps prevent errors like `'Delhi'` vs `'delhi'`.
- **Pros:**
  - The logic is very clear and easy to understand.
  - It doesn't depend on the Primary Key at all, only on the data you're interested in.
- **Cons:**
  - On extremely large tables (billions of rows), if the `From_City` and `To_City` columns are not indexed, this could be slightly slower than comparing indexed primary keys. For your purposes, this difference is zero.

#### Method 2: Comparing Train Numbers (`WHERE t1.TrainNo < t2.TrainNo`)

- **Reliability:** **Guaranteed.** The Primary Key (`TrainNo`) of a table is, by definition, **unique** for every row. This guarantees that for any two different rows, one `TrainNo` will always be less than the other (whether they are numbers or text). There is no possibility of a tie or ambiguity.
- **Pros:**
  - This is the standard, idiomatic SQL solution to this classic problem.
  - It is typically the fastest method because Primary Keys are always indexed.
- **Cons:**
  - The logic (`TrainNo < TrainNo`) is slightly less direct than comparing the cities you are actually interested in. It's a "technical trick" to break the tie rather than a "logical" one.

### Final Recommendation

**Both methods are excellent and will solve your problem correctly.**

1.  **For Performance and Standard Practice:** Use the **Train Number comparison** (`WHERE t1.TrainNo < t2.TrainNo`). This is what most experienced SQL developers would use.
2.  **For Logical Clarity:** Use the **City Name comparison** (`WHERE t1.From_City < t2.From_City`). It makes the query's intent to create an ordered pair very explicit.
