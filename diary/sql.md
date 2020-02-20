
### sql

### Parse dates
```sql
--culture could be  'en-GB'
PARSE ( string_value AS data_type [ USING culture ] )  
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
