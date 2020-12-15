---
layout: page
title: Sql
permalink: /diary/sql
---

### Parse dates
```sql
--culture could be  'en-GB'
PARSE ( string_value AS data_type [ USING culture ] )
-- or you can even parse a month and year only, defaulting to the 1st at 00:00:00
select PARSE('feb-2018' as datetime USING 'en-UK')
```
### Get duplicates or all equal entries of the same value
```sql
select count(*), myCol from [dbo].[myTable]
  group by myCol having COUNT(*) > 1 --For duplicates
```

### Move table from one schema to antoher (change ownership)
```sql
ALTER SCHEMA HumanResources TRANSFER Person.Address;
```
### Create a foreign key in an existing table
```sql
ALTER TABLE Sales.TempSalesReason
   ADD CONSTRAINT FK_TempSales_SalesReason FOREIGN KEY (TempID)
      REFERENCES Sales.SalesReason (SalesReasonID)
      ON DELETE CASCADE
      ON UPDATE CASCADE
;
```
### Create a foreign key in a new table
```sql
CREATE TABLE Sales.TempSalesReason
   (
      TempID int NOT NULL, Name nvarchar(50)
      , CONSTRAINT PK_TempSales PRIMARY KEY NONCLUSTERED (TempID)
      , CONSTRAINT FK_TempSales_SalesReason FOREIGN KEY (TempID)
        REFERENCES Sales.SalesReason (SalesReasonID)
        ON DELETE CASCADE
        ON UPDATE CASCADE
   )
;
```
### To create a primary key in an existing table
```sql
ALTER TABLE Production.TransactionHistoryArchive
   ADD CONSTRAINT PK_TransactionHistoryArchive_TransactionID PRIMARY KEY CLUSTERED (TransactionID);
```
### To create a primary key in a new table
```sql
CREATE TABLE Production.TransactionHistoryArchive1
   (
      TransactionID int IDENTITY (1,1) NOT NULL
      , CONSTRAINT PK_TransactionHistoryArchive_TransactionID PRIMARY KEY CLUSTERED (TransactionID)
   )
;
```
### Modify column’s data type
```sql
ALTER TABLE table_name
ALTER COLUMN column_name new_data_type(size);
```




### Granting INSERT permission on schema HumanResources to guest
```sql
GRANT INSERT ON SCHEMA :: HumanResources TO guest;
```
### Granting SELECT permission on schema Person to database user WilJo
```sql
GRANT SELECT ON SCHEMA :: Person TO WilJo;
```
### Renaming a schema
Execute the following
```sql
SELECT 'ALTER SCHEMA dbo TRANSFER ' + s.Name + '.' + o.Name
FROM sys.Objects o
INNER JOIN sys.Schemas s on o.schema_id = s.schema_id
WHERE s.Name = 'oldschema'
And (o.Type = 'U' Or o.Type = 'P' Or o.Type = 'V')
```
This query will result in output in the Results box which looks similar to the output below:
```sql
ALTER SCHEMA dbo TRANSFER yourschema.Table1
ALTER SCHEMA dbo TRANSFER yourschema.Table2
ALTER SCHEMA dbo TRANSFER yourschema.Table3
ALTER SCHEMA dbo TRANSFER yourschema.Table4
ALTER SCHEMA dbo TRANSFER yourschema.Table5
ALTER SCHEMA dbo TRANSFER yourschema.Table6
```
### Add column to table
```sql
alter table [dbo].[xxx] add  varchar(100) not null default 'BowWow'
--change type and constraints to your liking
```

### How to make a simple cursor to iterate over a table
```sql

declare @id int, @name varchar(200), @currency varchar(10), @country varchar(10), @lat decimal, @lon decimal, @code varchar(50)
declare c_stuff CURSOR for
SELECT  [id]
      ,[name]
      ,[currency]
      ,[country]
      ,[lat]
      ,[lon]
      ,[code]
FROM [myschema].[mytable]

OPEN c_stuff
FETCH NEXT FROM c_stuff
INTO @id, @name, @currency, @country, @lat, @lon, @code

WHILE @@FETCH_STATUS = 0
BEGIN
	PRINT CONCAT(@id,' ',@name,' ', @currency,' ', @country,' ', @lat,' ', @lon,' ', @code)
	FETCH NEXT FROM c_stuff INTO @id, @name, @currency, @country, @lat, @lon, @code
END
CLOSE c_stuff
DEALLOCATE  c_stuff
```

### Find and maybe delete duplicates in tables
Test data

Explanations of the ```over partition by``` clause can be found at microsoft [here](https://docs.microsoft.com/en-us/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver15)

```txt
EmployeeName
------------
Anand
Anand
Anil
Dipak
Anil
Dipak
Dipak
Anil
```

Example SQL
```sql
--just finding duplicates in a simple ways

select count(*),EmployeeName from Employee
group by EmployeeName
having count(*) > 1


select *
from (
  select *, rn=row_number() over (partition by EmployeeName order by empId)
  from Employee
) x
where rn > 1;

delete x from (
  select *, rn=row_number() over (partition by EmployeeName order by empId)
  from Employee
) x
where rn > 1;

--if the data entries have a unique identifier

delete from Employee
where ID not in
(
    select min(ID)
    from Employee
    group by EmployeeName
);
```

### nvarchar versus varchar
An nvarchar column can store any Unicode data. A varchar column is restricted to an 8-bit codepage. Some people think that varchar should be used because it takes up less space. I believe this is not the correct answer. Codepage incompatabilities are a pain, and Unicode is the cure for codepage problems. With cheap disk and memory nowadays, there is really no reason to waste time mucking around with code pages anymore.

All modern operating systems and development platforms use Unicode internally. By using nvarchar rather than varchar, you can avoid doing encoding conversions every time you read from or write to the database. Conversions take time, and are prone to errors. And recovery from conversion errors is a non-trivial problem.

### How do I find a stored procedure containing \<text\>?
```sql
SELECT OBJECT_NAME(id)
    FROM SYSCOMMENTS
    WHERE [text] LIKE '%Foo%'
    AND OBJECTPROPERTY(id, 'IsProcedure') = 1
    GROUP BY OBJECT_NAME(id)
```

### See and set implisit transaction on (and off)
```sql
DECLARE @IMPLICIT_TRANSACTIONS VARCHAR(3) = 'OFF';
IF ( (2 & @@OPTIONS) = 2 ) SET @IMPLICIT_TRANSACTIONS = 'ON';
SELECT @IMPLICIT_TRANSACTIONS AS IMPLICIT_TRANSACTIONS;

SET IMPLICIT_TRANSACTIONS ON;
```

### Drop Unique Constraint
The syntax for dropping a unique constraint in SQL Server is:
```SQL
ALTER TABLE table_name DROP CONSTRAINT constraint_name;
```
table_name. The name of the table to modify.
### Finding and killing a transaction on SQL-Server

In the SQL Server Management Studio, to find out details of the active transaction, execute following command
```SQL
DBCC opentran()
```
You will get the detail of the active transaction, then from the SPID of the active transaction, get the detail about the SPID using following commands
```SQL
exec sp_who2 <SPID>
exec sp_lock <SPID>
```

Now , you can kill that process using the following command
```SQL
kill <SPID>
```

### Find size (MB and rows) of all tables and indexes in you database
```SQL
SELECT
 t.NAME AS TableName,
 i.name AS indexName,
 SUM(p.rows) AS RowCounts,
 SUM(a.total_pages) AS TotalPages,
 SUM(a.used_pages) AS UsedPages,
 SUM(a.data_pages) AS DataPages,
 (SUM(a.total_pages) * 8) / 1024 AS TotalSpaceMB,
 (SUM(a.used_pages) * 8) / 1024 AS UsedSpaceMB,
 (SUM(a.data_pages) * 8) / 1024 AS DataSpaceMB
FROM
 sys.tables t
INNER JOIN
 sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN
 sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
INNER JOIN
 sys.allocation_units a ON p.partition_id = a.container_id
WHERE
 t.NAME NOT LIKE 'dt%' AND
 i.OBJECT_ID > 255 AND
 i.index_id <= 1
 GROUP BY
 t.NAME, i.object_id, i.index_id, i.name
ORDER BY
 OBJECT_NAME(i.object_id)
```

