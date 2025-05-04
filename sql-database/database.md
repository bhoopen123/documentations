
`Use AdventureWorksLT2019`

`SELECT * FROM SalesLT.Product`

`SELECT Name, Color, Size, Weight FROM SalesLT.Product`

-- having expression on a column in a query
```
SELECT name, color, size, Weight, 
LEFT(name, 2) as Initial,
CONCAT(ProductNumber, ' ', Color) AS ProductDescription,
2*Weight AS DoubleWeight,
Weight*Weight AS SquareOfWeight 
from SalesLT.Product
```

-- the default comparision in SQL is case-insensitive
`SELECT * FROM SalesLT.Customer`

```
SELECT DISTINCT FirstName, LastName
FROM SalesLT.Customer
```

-- < , > , <=, >=
-- =
-- != , <> are same
-- AND, OR, NOT
```
Select * FROM SalesLT.Product
where color = 'Black'
```

-- Don't use = or <> with null
-- because NULL = NULL will give asnwer as NULL
-- So, use 'is' operator instead
```
SELECT * FROM SalesLT.Product
WHERE Weight is not null
```

```
SELECT * FROM SalesLT.Product
Where Weight is not null and weight > 1000
``

-- is same as
SELECT * FROM SalesLT.Product
Where weight > 1000

-- IN operator
SELECT * FROM SalesLT.Product
WHERE ProductID IN (680,706,712)

-- BETWEEN (col>= minRange AND col <= maxRange)
SELECT * FROM SalesLT.Product
Where ListPrice between 10 and 100 -- lisPrice >= 10 and listPrice <= 100

SELECT SERVERPROPERTY('COLLATION')
SELECT DATABASEPROPERTYEX('AdventureWorks', 'Collation') SQLCollation;

-- Make a column Case sensitive in MsSQL
ALTER TABLE SalesLT.Product
	ALTER COLUMN Color VARCHAR (100)
	Collate Latin1_General_CS_AS 

-- apply Collation when creation a new table
Create table student
(
id int Identity, 
name varchar(100) collate Latin1_General_CS_AS
)

SELECT distinct name FROM student

-- Like operator
SELECT * FROM student WHERE name like 'A%'
-- % => 0 or more characters

SELECT * FROM student WHERE name like 'AB_'
-- _ => exactly one character

SELECT * FROM student WHERE name like '%\%%' ESCAPE '\'

-- escape character, search name contain '%'
SELECT * FROM student WHERE name like '%[%]%' 


-- Order By Ascending on 'Name'
SELECT * FROM student order by name

-- First orderBy on `FirstName` and then on `LastName` descending
SELECT * FROM SalesLT.Customer order by FirstName, LastName desc

SELECT TOP 10 * FROM SalesLT.Customer

-- OFFSET and FETCH
-- OFFSET and FETCH works with ORDER BY
SELECT * FROM SalesLT.Customer
ORDER BY CustomerID ASC OFFSET 30 ROWS
FETCH NEXT 10 ROWS ONLY;

-- GET SECOND MAX Price
SELECT DISTINCT ListPrice FROM SalesLT.Product
ORDER BY ListPrice DESC
OFFSET 1 ROWS FETCH NEXT 1 ROWS ONLY


SELECT * FROM Student
SELECT * FROM StudentPhone

--ALTER TABLE StudentPhone
--ADD PRIMARY KEY (id)

--ALTER TABLE student
--ADD PRIMARY KEY (id)

ALTER TABLE StudentPhone
ADD FOREIGN KEY (StudentId) REFERENCES Student(id)

SELECT * 
FROM student s
JOIN StudentPhone p 
ON s.id = p.StudentId

SELECT * 
FROM student,
StudentPhone 

-- SHOW, CREATE, DROP INDEXES
SELECT * FROM AdventureWorksLT2019.SalesLT.Customer

EXEC SP_HELPINDEX @objname = N'SalesLT.Customer'

SET SHOWPLAN_ALL ON; 
GO
SELECT * FROM SalesLT.Customer WHERE CustomerID = 29722;
GO
SET SHOWPLAN_ALL OFF;  
GO 

SET SHOWPLAN_ALL ON; 
GO
SELECT * FROM SalesLT.Customer WHERE Phone = '916-555-0137';
GO
SET SHOWPLAN_ALL OFF;  
GO

-- Rank Window Function
SELECT ProductID, Name, Color, ListPrice,
	ROW_NUMBER() OVER (order by ListPrice desc) AS RowNumber,
	Rank() OVER (ORDER BY ListPrice DESC ) AS Rank,
	DENSE_RANK() OVER (ORDER BY ListPrice DESC) AS DenseRank
FROM SalesLT.Product 


SELECT * 
FROM 
(
	SELECT ProductID, Name, Color, ListPrice,
		ROW_NUMBER() OVER (order by ListPrice desc) AS RowNumber,
		Rank() OVER (ORDER BY ListPrice DESC ) AS Rank,
		DENSE_RANK() OVER (ORDER BY ListPrice DESC) AS DenseRank
	FROM SalesLT.Product 

) tbl
where tbl.DenseRank = 4

-- use Partition By
SELECT DISTINCT Color FROM SalesLT.Product 

-- RETURN second highest price product for every color
SELECT * 
FROM 
(
	SELECT ProductID, Name, Color, ListPrice,
		ROW_NUMBER() OVER (PARTITION BY COLOR ORDER BY ListPrice DESC) AS RowNumber,
		Rank() OVER (PARTITION BY COLOR ORDER BY ListPrice DESC) AS Rank,
		DENSE_RANK() OVER (PARTITION BY COLOR ORDER BY ListPrice DESC) AS DenseRank
	FROM SalesLT.Product 

) tbl
where tbl.DenseRank = 2 -- for second highest
