---
layout: page
title: Sql (TSQL for mssql)
permalink: /diary/sql-mssql
---
## Parse dates
```sql
--culture could be  'en-GB'
PARSE ( string_value AS data_type [ USING culture ] )
-- or you can even parse a month and year only, defaulting to the 1st at 00:00:00
select PARSE('feb-2018' as datetime USING 'en-UK')
```
## Get duplicates or all equal entries of the same value
```sql
select count(*), myCol from [dbo].[myTable]
  group by myCol having COUNT(*) > 1 --For duplicates
```

## Move table from one schema to antoher (change ownership)
```sql
ALTER SCHEMA HumanResources TRANSFER Person.Address;
```
## Create a foreign key in an existing table
```sql
ALTER TABLE Sales.TempSalesReason
   ADD CONSTRAINT FK_TempSales_SalesReason FOREIGN KEY (TempID)
      REFERENCES Sales.SalesReason (SalesReasonID)
      ON DELETE CASCADE
      ON UPDATE CASCADE
;
```
## Create a foreign key in a new table
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
## To create a primary key in an existing table
```sql
ALTER TABLE Production.TransactionHistoryArchive
   ADD CONSTRAINT PK_TransactionHistoryArchive_TransactionID PRIMARY KEY CLUSTERED (TransactionID);
```
## To create a primary key in a new table
```sql
CREATE TABLE Production.TransactionHistoryArchive1
   (
      TransactionID int IDENTITY (1,1) NOT NULL
      , CONSTRAINT PK_TransactionHistoryArchive_TransactionID PRIMARY KEY CLUSTERED (TransactionID)
   )
;
```
## Modify column’s data type
```sql
ALTER TABLE table_name
ALTER COLUMN column_name new_data_type(size);
```




## Granting INSERT permission on schema HumanResources to guest
```sql
GRANT INSERT ON SCHEMA :: HumanResources TO guest;
```
## Granting SELECT permission on schema Person to database user WilJo
```sql
GRANT SELECT ON SCHEMA :: Person TO WilJo;
```
## Renaming a schema
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
## Add column to table
```sql
alter table [dbo].[xxx] add  varchar(100) not null default 'BowWow'
--change type and constraints to your liking
```

## How to make a simple cursor to iterate over a table
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

## Find and maybe delete duplicates in tables
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

## nvarchar versus varchar
An nvarchar column can store any Unicode data. A varchar column is restricted to an 8-bit codepage. Some people think that varchar should be used because it takes up less space. I believe this is not the correct answer. Codepage incompatabilities are a pain, and Unicode is the cure for codepage problems. With cheap disk and memory nowadays, there is really no reason to waste time mucking around with code pages anymore.

All modern operating systems and development platforms use Unicode internally. By using nvarchar rather than varchar, you can avoid doing encoding conversions every time you read from or write to the database. Conversions take time, and are prone to errors. And recovery from conversion errors is a non-trivial problem.

## How do I find a stored procedure containing \<text\>?
```sql
SELECT OBJECT_NAME(id)
    FROM SYSCOMMENTS
    WHERE [text] LIKE '%Foo%'
    AND OBJECTPROPERTY(id, 'IsProcedure') = 1
    GROUP BY OBJECT_NAME(id)
```

## See and set implicit transaction on (and off)
```sql
DECLARE @IMPLICIT_TRANSACTIONS VARCHAR(3) = 'OFF';
IF ( (2 & @@OPTIONS) = 2 ) SET @IMPLICIT_TRANSACTIONS = 'ON';
SELECT @IMPLICIT_TRANSACTIONS AS IMPLICIT_TRANSACTIONS;

SET IMPLICIT_TRANSACTIONS ON;
```

## Drop Unique Constraint
The syntax for dropping a unique constraint in SQL Server is:
```SQL
ALTER TABLE table_name DROP CONSTRAINT constraint_name;
```
table_name. The name of the table to modify.
## Finding and killing a transaction on SQL-Server

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

## Find size (MB and rows) of all tables and indexes in you database
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

## Find just the number of rows of all tables in a database
```sql
 SELECT
      QUOTENAME(SCHEMA_NAME(sOBJ.schema_id)) + '.' + QUOTENAME(sOBJ.name) AS [TableName]
      , SUM(sPTN.Rows) AS [RowCount]
FROM
      sys.objects AS sOBJ
      INNER JOIN sys.partitions AS sPTN
            ON sOBJ.object_id = sPTN.object_id
WHERE
      sOBJ.type = 'U'
      AND sOBJ.is_ms_shipped = 0x0
      AND index_id < 2 -- 0:Heap, 1:Clustered
GROUP BY
      sOBJ.schema_id
      , sOBJ.name
ORDER BY [TableName]
GO
```

## Find gaps in sequences in tables in database
```SQL
SELECT  TOP 1
        id + 1
FROM    mytable mo
WHERE   NOT EXISTS
        (
        SELECT  NULL
        FROM    mytable mi
        WHERE   mi.id = mo.id + 1
        )
ORDER BY
        id
```

## Date and Time Conversions Using SQL Server


|Format #|Query|Sample|
|---|---|---|
|1|select convert(varchar, getdate(), 1)|12/30/06|
|2|select convert(varchar, getdate(), 2)|06.12.30|
|3|select convert(varchar, getdate(), 3)|30/12/06|
|4|select convert(varchar, getdate(), 4)|30.12.06|
|5|select convert(varchar, getdate(), 5)|30-12-06|
|6|select convert(varchar, getdate(), 6)|30 Dec 06|
|7|select convert(varchar, getdate(), 7)|Dec 30, 06|
|10|select convert(varchar, getdate(), 10)|12-30-06|
|11|select convert(varchar, getdate(), 11)|06/12/30|
|12|select convert(varchar, getdate(), 12)|061230|
|23|select convert(varchar, getdate(), 23)|2006-12-30|
|101|select convert(varchar, getdate(), 101)|12/30/2006|
|102|select convert(varchar, getdate(), 102)|2006.12.30|
|103|select convert(varchar, getdate(), 103)|30/12/2006|
|104|select convert(varchar, getdate(), 104)|30.12.2006|
|105|select convert(varchar, getdate(), 105)|30-12-2006|
|106|select convert(varchar, getdate(), 106)|30 Dec 2006|
|107|select convert(varchar, getdate(), 107)|Dec 30, 2006|
|110|select convert(varchar, getdate(), 110)|12-30-2006|
|111|select convert(varchar, getdate(), 111)|2006/12/30|
|112|select convert(varchar, getdate(), 112)|20061230|
|8|select convert(varchar, getdate(), 8)|00:38:54|
|14|select convert(varchar, getdate(), 14)|00:38:54:840|
|24|select convert(varchar, getdate(), 24)|00:38:54|
|108|select convert(varchar, getdate(), 108)|00:38:54|
|114|select convert(varchar, getdate(), 114)|00:38:54:840|
|0|select convert(varchar, getdate(), 0)|Dec 12 2006 12:38AM|
|9|select convert(varchar, getdate(), 9)|Dec 30 2006 12:38:54:840AM|
|13|select convert(varchar, getdate(), 13)|30 Dec 2006 00:38:54:840AM|
|20|select convert(varchar, getdate(), 20)|2006-12-30 00:38:54|
|21|select convert(varchar, getdate(), 21)|2006-12-30 00:38:54.840|
|22|select convert(varchar, getdate(), 22)|12/30/06 12:38:54 AM|
|25|select convert(varchar, getdate(), 25)|2006-12-30 00:38:54.840|
|100|select convert(varchar, getdate(), 100)|Dec 30 2006 12:38AM|
|109|select convert(varchar, getdate(), 109)|Dec 30 2006 12:38:54:840AM|
|113|select convert(varchar, getdate(), 113)|30 Dec 2006 00:38:54:840|
|120|select convert(varchar, getdate(), 120)|2006-12-30 00:38:54|
|121|select convert(varchar, getdate(), 121)|2006-12-30 00:38:54.840|
|126|select convert(varchar, getdate(), 126)|2006-12-30T00:38:54.840|
|127|select convert(varchar, getdate(), 127)|2006-12-30T00:38:54.840|
|130|select convert(nvarchar, getdate(), 130)|date output|
|131|select convert(nvarchar, getdate(), 131)|10/12/1427 12:38:54:840AM|

You can also format the date or time without dividing characters, as well as concatenate the date and time string:

|Sample statement|Output|
|---|---|
|select replace(convert(varchar, getdate(),101),'/','')|12302006|
|select replace(convert(varchar, getdate(),101),'/','') + replace(convert(varchar, getdate(),108),':','')|12302006004426|

```SQL
DECLARE @counter INT = 0
DECLARE @date DATETIME = '2006-12-30 00:38:54.840'

CREATE TABLE #dateFormats (dateFormatOption int, dateOutput nvarchar(40))

WHILE (@counter <= 150 )
BEGIN
   BEGIN TRY
      INSERT INTO #dateFormats
      SELECT CONVERT(nvarchar, @counter), CONVERT(nvarchar,@date, @counter)
      SET @counter = @counter + 1
   END TRY
   BEGIN CATCH;
      SET @counter = @counter + 1
      IF @counter >= 150
      BEGIN
         BREAK
      END
   END CATCH
END

SELECT * FROM #dateFormats
```
## Further reading

[Determine SQL Server Date and Time Parts with DATEPART and DATENAME Functions](https://www.mssqltips.com/sqlservertip/2507/determine-sql-server-date-and-time-parts-with-datepart-and-datename-functions/)

[SQL Server Date and Time Data Types](https://www.mssqltips.com/sqlservertip/1616/sql-server-2008-date-and-time-data-types/)

[SQL Server function to convert integer date to datetime format](https://www.mssqltips.com/sqlservertip/1712/sql-server-function-to-convert-integer-date-to-datetime-format/)

[SQL Server DateTime Best Practices](https://www.mssqltips.com/sqlservertip/5206/sql-server-datetime-best-practices/)

[Format SQL Server Dates with FORMAT Function](https://www.mssqltips.com/sqlservertip/2655/format-sql-server-dates-with-format-function/)


## Find procedure which contains a certain string
```sql
DECLARE @SearchText varchar(1000) = 'serie_id';

SELECT DISTINCT SPName
FROM (
    (SELECT ROUTINE_NAME SPName
        FROM INFORMATION_SCHEMA.ROUTINES
        WHERE ROUTINE_DEFINITION LIKE '%' + @SearchText + '%'
        AND ROUTINE_TYPE='PROCEDURE')
    UNION ALL
    (SELECT OBJECT_NAME(id) SPName
        FROM SYSCOMMENTS
        WHERE [text] LIKE '%' + @SearchText + '%'
        AND OBJECTPROPERTY(id, 'IsProcedure') = 1
        GROUP BY OBJECT_NAME(id))
    UNION ALL
    (SELECT OBJECT_NAME(object_id) SPName
        FROM sys.sql_modules
        WHERE OBJECTPROPERTY(object_id, 'IsProcedure') = 1
        AND definition LIKE '%' + @SearchText + '%')
) AS T
ORDER BY T.SPName
```

## Find queries which take the most resources
```SQL
SELECT TOP 30
    SUBSTRING(qt.TEXT, (qs.statement_start_offset/2)+1,
((CASE qs.statement_end_offset
WHEN -1 THEN DATALENGTH(qt.TEXT)
ELSE qs.statement_end_offset
END - qs.statement_start_offset)/2)+1),
    qs.execution_count,
    qs.total_logical_reads, qs.last_logical_reads,
    qs.total_logical_writes, qs.last_logical_writes,
    qs.total_worker_time,
    qs.last_worker_time,
    qs.total_elapsed_time/1000000 total_elapsed_time_in_S,
    qs.last_elapsed_time/1000000 last_elapsed_time_in_S,
    qs.last_execution_time,
    qp.query_plan
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle) qp
ORDER BY qs.total_logical_reads DESC
-- logical reads
-- ORDER BY qs.total_logical_writes DESC -- logical writes
-- ORDER BY qs.total_worker_time DESC -- CPU time
```
## Working with extended propperties for ducumentation
Here is a great [link](https://www.mssqltips.com/sqlservertip/5384/working-with-sql-server-extended-properties/) for that.
In essence you can use the like this:
```sql
EXEC sys.sp_addextendedproperty @name = N'Description', @value = N'Some description', @level0type = N'SCHEMA', @level0name = schema_name, @level1type = N'TABLE',  @level1name = table_name;
```

## Finding all cached queries
```sql
SELECT cplan.usecounts, cplan.objtype, qtext.text, qplan.query_plan
FROM sys.dm_exec_cached_plans AS cplan
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS qtext
CROSS APPLY sys.dm_exec_query_plan(plan_handle) AS qplan
ORDER BY cplan.usecounts DESC
```

## Error 845
This error could be due to deadlocks and can be remided by setting it to [another value than 0](https://protect2.fireeye.com/v1/url?k=31323334-501d5122-fe22d327-454445555731-a2c730dadeb4ff8c&q=1&e=d2e7866e-498b-4488-b685-c24bb34c5ed7&u=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-sql%2Fdatabase%2Fconfigure-max-degree-of-parallelism%23overview)
```sql
--se the current setting
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
GO
--Change the setting
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8
```
