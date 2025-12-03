---
title: "SQL Set Behavior in Subquery Rewinds"
date: 2023-07-01
layout: single
categories:
  - Technical
tags:
  - T-SQL
  - Performance
---

## SQL Set Behavior in Subquery Rewinds

I once heard Itzik Ben-Gan speak about the theory of set based operations in SQL that the SQL engine is supposed to treat sets consistently in all types of operations. For example, SQL Server will not guarantee the order of results even though it may appear to consistently retrieve them in the order of the first column in a set or in the order of a primary key column on a table.  So I thought I would share what at least appears to be an inconsistency in SQL Server's behavior with regards to sets.

Let's look at the following script to show how temp tables and table variables can behave differently:

```sql
USE tempdb;
```

## Temp Table Setup

```sql
CREATE TABLE #AwardList (PointThreshold int, Award varchar(50));
INSERT INTO #AwardList (PointThreshold, Award) VALUES
(5,'Troll'),
(5,'Goblin'),
(5,'Dwarf'),
(5,'Elf'),
(5,'Halfling'),
(10,'Paladin'),
(10,'Warrior'),
(10,'Ranger'),
(10,'Wizard'),
(10,'Illusionist');

CREATE TABLE #AwardPoints (ID int, Points int);
INSERT INTO #AwardPoints (ID, Points) VALUES
(1,5),
(2,5),
(3,10),
(4,10),
(5,10);
GO
```

## Table Variable Setup

```sql
DECLARE @AwardList TABLE (PointThreshold int, Award varchar(50));
DECLARE @AwardPoints TABLE(ID int, Points int);
INSERT INTO @AwardPoints (ID, Points) VALUES
(1,5),
(2,5),
(3,10),
(4,10),
(5,10);

INSERT INTO @AwardList (PointThreshold, Award) VALUES
(5,'Troll'),
(5,'Goblin'),
(5,'Dwarf'),
(5,'Elf'),
(5,'Halfling'),
(10,'Paladin'),
(10,'Warrior'),
(10,'Ranger'),
(10,'Wizard'),
(10,'Illusionist');

```

### Produces Semi-Random Results (1)

```sql
SELECT
  ap.ID,
  (
    SELECT TOP 1 al.Award
    FROM @AwardList al
    WHERE al.PointThreshold = ap.Points
    ORDER BY NEWID()
  )
FROM @AwardPoints ap
```

### Produces Non-Random Results (1)

```sql
SELECT
  ap.ID,
  (
    SELECT TOP 1 al.Award
    FROM #AwardList al
    WHERE al.PointThreshold = ap.Points
    ORDER BY NEWID()
  )
FROM #AwardPoints ap

GO
```

### Produces Semi-Random Results (2)

```sql
DECLARE @AwardPoints TABLE (ID int, Points int);
INSERT INTO @AwardPoints (ID, Points) VALUES
(1,5),
(2,5),
(3,10),
(4,10),
(5,10);

SELECT
  ap.ID,
  (
    SELECT TOP 1 al.Award
    FROM #AwardList al
    WHERE al.PointThreshold = ap.Points
    ORDER BY NEWID()
  )
FROM @AwardPoints ap

GO
```

### Produces Non-Random Results (2)

```sql
DECLARE @AwardList TABLE (PointThreshold int, Award varchar(50));
INSERT INTO @AwardList (PointThreshold, Award) VALUES
(5,'Troll'),
(5,'Goblin'),
(5,'Dwarf'),
(5,'Elf'),
(5,'Halfling'),
(10,'Paladin'),
(10,'Warrior'),
(10,'Ranger'),
(10,'Wizard'),
(10,'Illusionist');

SELECT
  ap.ID,
  (
    SELECT TOP 1 al.Award
    FROM @AwardList al
    WHERE al.PointThreshold = ap.Points
    ORDER BY NEWID()
  )
FROM #AwardPoints ap

GO
```

### Produces Non-Random Results (3)

```sql
SELECT
  ap.ID, al.Award
FROM #AwardPoints ap
CROSS APPLY
(
  SELECT TOP 1 al.Award
  FROM #AwardList al
  WHERE al.PointThreshold = ap.Points
  ORDER BY NEWID()
) al
```

Notice that when the temp table is in the basis position of the query (in the FROM clause), the results will not be random, but if a table variable is used in the basis position, the results will be reasonably random.

Understanding this phenomenon begins with the SQL Server engine. First, let's note that different functions behave differently depending on the circumstances. For example, given a base table of Customers, if I run the following query `(SELECT TOP 20 CustomerID, GETDATE(), NEWID() FROM dbo.Customers)`, I'll get the top 20 customer IDs from the Customer table with a single date/time value for the entire set, and 20 distinct uniqueidentifier values.
So in the above script, in both the `CROSS APPLY` statement and the sub-query statement, using `ORDER BY NEWID()` will produce a distinct set of uniqueidentifier values for the sub-query.  What's different is that in one scenario, the sub-query is being effectively run once and its result is being re-used across the remaining matches from the base query.  In the other scenario, the sub-query is actually being rerun for each row from the base query.  This distinction hinges on the base query being rooted in a temp table vs a table variable and the fact that the temp table is disc based whereas the table variable resides solely in memory, at least for the purposes of determining query behavior.

To understand why this behavior is what it is, you have to think about what the SQL Server engine is optimized to do.  When it accesses the disc I/O subsystem, there is an intrinsic cost, so the engine attempts to optimize query performance by not forcing a sub-query to re-run for each row when the match is the same against the base query and the base query is against a disc based table.  When the base query is against a memory based table, there is no such assumption about intrinsic cost regardless of the source of the sub-query.

So, if you aren't using table variables, how can you get around this behavior?
One way is to explode the set initially.  You can do a simple join on both #AwardList and #AwardPoints and add a random value to each row in the exploded set.  You can use the `NEWID()` function, but I prefer something more like:

`CONVERT(int, CRYPT_GEN_RANDOM(2)) AS RandomID`

Next, you want to use a CTE to filter the exploded set by getting the `MAX(RandomID)` for each ID value, which is accomplished simply by using the `GROUP BY` clause on the ID column.  Now you can join back on the exploded set to get your random award for each ID by joining on the ID and RandomID columns.
