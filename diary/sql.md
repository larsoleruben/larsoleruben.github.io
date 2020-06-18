
### sql

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

### Find and naybe delete duplicates in tables
Test data

Explanations ov the ```over partition by´´´ clause can be found at microsoft [here](https://docs.microsoft.com/en-us/sql/t-sql/queries/select-over-clause-transact-sql?view=sql-server-ver15)

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

