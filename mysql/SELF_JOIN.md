# SELF JOIN in MySQL

A SELF JOIN is a join where a table is joined with itself. This is useful for comparing rows within the same table or finding relationships between rows in the same table.

## Example Scenario: Train Connections

We'll use a train database to demonstrate SELF JOIN. The `train_info` table contains train details, and we'll find connecting trains where one train's destination is another's source.

### Table Schema

```sql
CREATE TABLE train_info (
    TrainNo INTEGER PRIMARY KEY,
    TrainName TEXT NOT NULL,
    SourceStation TEXT NOT NULL,
    DestinationStation TEXT NOT NULL,
    DepartureTime TEXT NOT NULL,
    ArrivalTime TEXT NOT NULL,
    TravelDays INTEGER NOT NULL,
    TrainType TEXT NOT NULL
);
```

### Sample Data

```sql
INSERT INTO train_info (TrainNo, TrainName, SourceStation, DestinationStation, DepartureTime, ArrivalTime, TravelDays, TrainType) VALUES
(12309, 'Rajdhani Express', 'RJPB', 'NDLS', '19:10', '07:40', 2, 'Rajdhani'),
(12024, 'Jan Shatabdi Express', 'PNBE', 'HWH', '05:30', '13:25', 1, 'Jan Shatabdi'),
(12565, 'Bihar Sampark Kranti', 'DBG', 'NDLS', '08:25', '05:15', 2, 'Sampark Kranti'),
(12397, 'Mahabodhi SF Express', 'GAYA', 'NDLS', '14:00', '04:10', 2, 'Superfast'),
(11062, 'Pawan Express', 'MFP', 'LTT', '16:30', '03:40', 3, 'Express'),
(82355, 'Patna-CSMT Suvidha', 'PNBE', 'CSMT', '13:00', '14:50', 2, 'Suvidha'),
(12506, 'North East Express', 'ANVT', 'KYQ', '07:40', '16:25', 2, 'Superfast'),
(12389, 'Gaya-Chennai Egmore SF', 'GAYA', 'MAS', '05:30', '16:10', 2, 'Superfast'),
(12577, 'Bagmati SF Express', 'DBG', 'MYS', '15:45', '17:45', 3, 'Superfast'),
(22353, 'Patna-SMVB Humsafar', 'PNBE', 'SMVB', '20:25', '17:25', 3, 'Humsafar'),
(12423, 'Rajdhani Express', 'DBRG', 'NDLS', '20:55', '10:30', 2, 'Rajdhani');
```

### SELF JOIN Query: Finding Connecting Trains

To find pairs of trains where one train's destination is another's source (potential connections):

```sql
SELECT
    t1.TrainName AS ArrivingTrain,
    t1.SourceStation AS StartPoint,
    t1.DestinationStation AS Hub,
    t2.TrainName AS DepartingTrain,
    t2.DestinationStation AS FinalDestination
FROM
    train_info AS t1
JOIN
    train_info AS t2 ON t1.DestinationStation = t2.SourceStation
WHERE
    t1.TrainNo != t2.TrainNo;
```

### Explanation

- `t1` and `t2` are aliases for the same table.
- The join condition `t1.DestinationStation = t2.SourceStation` finds connections.
- The WHERE clause ensures we don't connect a train to itself.

### Sample Result

| ArrivingTrain        | StartPoint | Hub  | DepartingTrain     | FinalDestination |
| -------------------- | ---------- | ---- | ------------------ | ---------------- |
| Rajdhani Express     | RJPB       | NDLS | North East Express | KYQ              |
| Bihar Sampark Kranti | DBG        | NDLS | North East Express | KYQ              |
| ...                  | ...        | ...  | ...                | ...              |

## Additional Practice Queries

### 1. Basic Filtering

```sql
SELECT TrainName, SourceStation, DepartureTime, TrainType
FROM train_info
WHERE DestinationStation = 'NDLS'
ORDER BY DepartureTime;
```

### 2. Aggregation

```sql
SELECT SourceStation, COUNT(TrainNo) AS NumberOfTrains
FROM train_info
GROUP BY SourceStation
ORDER BY NumberOfTrains DESC;
```

### 3. Advanced Filtering

```sql
SELECT TrainName, SourceStation, DestinationStation, TrainType
FROM train_info
WHERE TrainType IN ('Rajdhani', 'Superfast');
```

This example demonstrates how SELF JOIN can be used to find relationships within a single table.
