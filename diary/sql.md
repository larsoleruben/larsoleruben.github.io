
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
### Granting INSERT permission on schema HumanResources to guest
```sql
GRANT INSERT ON SCHEMA :: HumanResources TO guest;
```
### Granting SELECT permission on schema Person to database user WilJo
```sql
GRANT SELECT ON SCHEMA :: Person TO WilJo WITH GRANT OPTION; 
```
