SELF JOIN

Of course\! Creating a database centered on trains from Bihar is a fantastic way to practice SQL with data that's relatable and realistic.

Here is a `train_info` table schema, populated with famous and important trains originating from or passing through major stations in Bihar, connecting to various well-known places across India. The data is designed to be perfect for practicing all sorts of queries, including aggregations, filtering, and even `SELF JOIN` concepts.

### 1\. Table Schema (`CREATE TABLE`)

This schema captures essential information about each train service.

```sql
CREATE TABLE train_info (
    TrainNo INTEGER PRIMARY KEY,         -- The unique train number
    TrainName TEXT NOT NULL,             -- The official name of the train
    SourceStation TEXT NOT NULL,         -- The train's origin station code
    DestinationStation TEXT NOT NULL,    -- The train's final destination code
    DepartureTime TEXT NOT NULL,         -- Departure time from the source station (HH:MM)
    ArrivalTime TEXT NOT NULL,           -- Arrival time at the destination station (HH:MM)
    TravelDays INTEGER NOT NULL,         -- Number of days the journey takes (1, 2, 3, etc.)
    TrainType TEXT NOT NULL              -- Category of the train (e.g., Superfast, Humsafar)
);
```

### 2\. Table Data (`INSERT`)

This data includes a mix of train types, routes, and timings, all connected to Bihar. I've also included a famous train to your city, Guwahati\!

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

_(Station Codes: RJPB/PNBE-Patna, DBG-Darbhanga, GAYA-Gaya, MFP-Muzaffarpur, NDLS-New Delhi, HWH-Howrah/Kolkata, LTT/CSMT-Mumbai, ANVT-Anand Vihar/Delhi, KYQ-Kamakhya/Guwahati, MAS-Chennai, MYS-Mysuru, SMVB-Bengaluru, DBRG-Dibrugarh)_

Storing the station details in a separate table and linking them is called **normalization**. It makes the data cleaner, easier to manage, and reduces redundancy.

Here is the new `stations` table, designed to work perfectly with your `train_info` table.

### 1\. Table Schema (`CREATE TABLE`)

This table will store the station code, its full name, the main city, and the state.

```sql
CREATE TABLE stations (
    StationCode TEXT PRIMARY KEY,
    StationName TEXT NOT NULL,
    City TEXT NOT NULL,
    State TEXT NOT NULL
);
```

### 2\. Table Data (`INSERT`)

Here are the `INSERT` statements to populate the `stations` table based on the codes from your `train_info` table. I've added a row for each unique code.

```sql
INSERT INTO stations (StationCode, StationName, City, State) VALUES
('RJPB', 'Rajendra Nagar Terminal', 'Patna', 'Bihar'),
('PNBE', 'Patna Junction', 'Patna', 'Bihar'),
('DBG', 'Darbhanga Junction', 'Darbhanga', 'Bihar'),
('GAYA', 'Gaya Junction', 'Gaya', 'Bihar'),
('MFP', 'Muzaffarpur Junction', 'Muzaffarpur', 'Bihar'),
('NDLS', 'New Delhi', 'New Delhi', 'Delhi'),
('HWH', 'Howrah Junction', 'Kolkata', 'West Bengal'),
('LTT', 'Lokmanya Tilak Terminus', 'Mumbai', 'Maharashtra'),
('CSMT', 'C.S. Maharaj Terminus', 'Mumbai', 'Maharashtra'),
('ANVT', 'Anand Vihar Terminal', 'New Delhi', 'Delhi'),
('KYQ', 'Kamakhya Junction', 'Guwahati', 'Assam'),
('MAS', 'MGR Chennai Central', 'Chennai', 'Tamil Nadu'),
('MYS', 'Mysuru Junction', 'Mysuru', 'Karnataka'),
('SMVB', 'SMVT Bengaluru', 'Bengaluru', 'Karnataka'),
('DBRG', 'Dibrugarh', 'Dibrugarh', 'Assam');
```

### Your New `stations` Table

After running the commands, your table will look like this:

| StationCode | StationName               | City        | State         |
| :---------- | :------------------------ | :---------- | :------------ |
| RJPB        | Rajendra Nagar Terminal   | Patna       | Bihar         |
| PNBE        | Patna Junction            | Patna       | Bihar         |
| DBG         | Darbhanga Junction        | Darbhanga   | 

### How to Use This Table with a `JOIN`

Now, to see the full city names in your query results, you need to `JOIN` the `train_info` table with this new `stations` table. Since you need to get names for both the source and the destination, you must join the `stations` table **twice**.

This is a classic and very important SQL concept.

**Example Query:** Find all trains, showing their full source and destination city names.

```sql
SELECT
    ti.TrainName,
    ti.TrainType,
    source_station.City AS SourceCity,
    dest_station.City AS DestinationCity
FROM
    train_info AS ti
JOIN
    stations AS source_station ON ti.SourceStation = source_station.StationCode
JOIN
    stations AS dest_station ON ti.DestinationStation = dest_station.StationCode;
```

**Explanation:**

1.  We use aliases (`ti`, `source_station`, `dest_station`) to keep the query readable.
2.  The first `JOIN` connects `train_info.SourceStation` to `stations.StationCode` to get the source city's name. We alias this instance of the `stations` table as `source_station`.
3.  The second `JOIN` connects `train_info.DestinationStation` to `stations.StationCode` to get the destination city's name. We use a different alias, `dest_station`, for this instance.

**Result of the Example Query:**

| TrainName              | TrainType      | SourceCity  | DestinationCity |
| :--------------------- | :------------- | :---------- | :-------------- |
| Rajdhani Express       | Rajdhani       | Patna       | New Delhi       |
| Jan Shatabdi Express   | Jan Shatabdi   | Patna       | Kolkata         |
| Bihar Sampark Kranti   | Sampark Kranti | Darbhanga   | New Delhi       |


You now have a well-structured set of tables to practice powerful `JOIN` queries\!



---

### Practice Queries to Get You Started

Here are some queries you can run on this table to practice different concepts.

#### 1\. Basic Filtering: Find all trains to New Delhi (NDLS)

```sql
SELECT TrainName, SourceStation, DepartureTime, TrainType
FROM train_info
WHERE DestinationStation = 'NDLS'
ORDER BY DepartureTime;
```

#### 2\. Aggregation: Count how many listed trains start from each source station

```sql
SELECT SourceStation, COUNT(TrainNo) AS NumberOfTrains
FROM train_info
GROUP BY SourceStation
ORDER BY NumberOfTrains DESC;
```

#### 3\. Advanced Filtering: Find all "Rajdhani" or "Superfast" trains

```sql
SELECT TrainName, SourceStation, DestinationStation, TrainType
FROM train_info
WHERE TrainType = 'Rajdhani' OR TrainType = 'Superfast';
```

#### 4\. The Self-Join Challenge: Find "Connecting Hubs"

**Goal:** Find pairs of trains where one train's destination city is another train's source city. This could represent a potential (though not time-guaranteed) connection.

**Logic:** We join `train_info` to itself. The condition is that the `DestinationStation` of the first train (`t1`) matches the `SourceStation` of the second train (`t2`). This identifies cities that act as hubs in our dataset.

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
    t1.TrainNo != t2.TrainNo; -- Ensure we are not connecting a train to itself
```

This query will show you, for example, that you can arrive in New Delhi (NDLS) via the Rajdhani from Patna and then potentially depart from a Delhi station (ANVT) on the North East Express.

This dataset gives you a solid foundation to explore many SQL concepts with a fun, local theme. Enjoy your practice\!
