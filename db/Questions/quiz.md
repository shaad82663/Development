# SQL Server Interview Questions and Answers

→ For more Step by Step and Interview Q&A Course visit - [www.questpond.com](http://www.questpond.com)  
→ For offline training visit - [https://www.stepbystepschools.net/](https://www.stepbystepschools.net/)  
→ Stay tuned with Questpond for more updates - [t.me/questpond](http://t.me/questpond)

## Contents
1. Explain normalization?
2. How to implement normalization?
3. What is denormalization?
4. Explain OLTP vs OLAP?
5. Explain 1st, 2nd, and 3rd Normal Form?
6. Primary Key vs Unique Key?
7. Differentiate between Char vs Varchar?
8. Differentiate between Char vs NChar?
9. What’s the size of Char vs NChar?
10. What is the use of Index?
11. How does it make search faster?
12. What are the two types of Indexes?
13. Clustered vs Non-Clustered Index?
14. Function vs Stored Procedure?
15. What are triggers and why do you need them?
16. What are types of triggers?
17. Differentiate between After Trigger vs Instead Of Trigger?
18. What is the need for Identity?
19. Explain transactions and how to implement them?
20. What are Inner Joins?
21. Explain Left Join?
22. Explain Right Join?
23. Explain Full Outer Join?
24. Explain Cross Join?
25. Why do we need UNION?
26. Differentiate between Union vs Union All?
27. Can we have unequal columns in Union?
28. Can columns have different data types in Union?
29. Which aggregate functions have you used?
30. When to use Group By?
31. Can we select columns not part of Group By?
32. What is the Having clause?
33. Having clause vs Where clause?
34. How can we sort records?
35. What’s the default sort?
36. How can we remove duplicates?
37. Select the first top X records?
38. How to handle NULLs?
39. What is the use of wildcards?
40. What is the use of Alias?
41. How to write a Case statement?
42. What are self-referential tables?
43. What is a self-join?
44. Explain the Between clause?
45. Explain Subquery?
46. Can inner Subquery return multiple results?
47. What is a Correlated Query?
48. Differentiate between Joins and Subquery?
49. Performance: Joins vs Subquery?
50. Select the top nth highest salary using TOP and ORDER BY?
51. Select the top nth highest salary using Correlated Queries?
52. Select top nth using T-SQL?
53. Performance comparison of all methods for top nth salary?
54. What is a CTE?
55. Can we execute CTE multiple times?
56. What is the use of CTE?
57. How to write a recursive CTE?
58. Can we see some real-world examples of CTE?
59. Can we perform insert/updates on CTEs?
60. Does CTE update the tables physically?
61. What are temporary tables?
62. Temp tables vs CTE?
63. Performance: CTE vs Temp tables?
64. What are window functions in SQL?
65. What does the PARTITION BY clause do in window functions?
66. Is PARTITION BY similar to GROUP BY?
67. What is the difference between RANK vs DENSE_RANK?
68. Find duplicate records from a table?
69. Find unique records?
70. Delete duplicate records (with ID)?
71. Delete duplicate records (without ID)?
72. Delete duplicate records (using CTE)?

## 1. Explain normalization?
Normalization removes redundant data to improve data integrity and reduce storage.  
**Example**: Split a table with customer and order data into separate `Customers` and `Orders` tables.

## 2. How to implement normalization?
Split tables into master (reference) and transactional tables with foreign keys.  
**Example**:  
```sql
CREATE TABLE Customers (CustomerID INT PRIMARY KEY, Name VARCHAR(50));
CREATE TABLE Orders (OrderID INT PRIMARY KEY, CustomerID INT, FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID));
```

## 3. What is denormalization?
Merging tables to reduce joins and improve query performance.  
**Example**: Combine `Customers` and `Orders` into one table for faster reads in reporting.

## 4. Explain OLTP vs OLAP?
- **OLTP**: Normalized for transactional efficiency (e.g., e-commerce).  
- **OLAP**: Denormalized for analytical queries (e.g., data warehousing).

## 5. Explain 1st, 2nd, and 3rd Normal Form?
- **1NF**: Atomic values, no repeating groups.  
  **Example**: Split `Phone1, Phone2` into a separate `Phones` table.  
- **2NF**: 1NF + non-key columns fully dependent on primary key.  
  **Example**: Move partial dependencies (e.g., `ProductName` in `Orders`) to a `Products` table.  
- **3NF**: 2NF + no transitive dependencies.  
  **Example**: Move `CustomerAddress` from `Orders` to `Customers`.

## 6. Primary Key vs Unique Key?
- **Primary Key**: No NULLs, one per table.  
- **Unique Key**: Allows NULLs, multiple per table.

## 7. Differentiate between Char vs Varchar?
- **Char**: Fixed length, padded with spaces.  
- **Varchar**: Variable length, no padding.  
**Example**: `CHAR(5)` for "abc" stores "abc  ", `VARCHAR(5)` stores "abc".

## 8. Differentiate between Char vs NChar?
- **Char**: For English characters.  
- **NChar**: For multilingual (Unicode) characters.

## 9. What’s the size of Char vs NChar?
- **Char**: 1 byte per character.  
- **NChar**: 2 bytes per character.

## 10. What is the use of Index?
Indexes speed up data retrieval.  
**Example**:  
```sql
CREATE INDEX idx_name ON Customers(Name);
```

## 11. How does it make search faster?
Uses B-tree/B+ tree for quick lookups.  
**Example**: Searching `Name='John'` uses index instead of scanning entire table.

## 12. What are the two types of Indexes?
Clustered and Non-Clustered.

## 13. Clustered vs Non-Clustered Index?
- **Clustered**: Stores actual data in index order (1 per table).  
- **Non-Clustered**: Separate structure pointing to data (multiple allowed).  
**Example**:  
```sql
CREATE CLUSTERED INDEX idx_id ON Customers(CustomerID);
CREATE NONCLUSTERED INDEX idx_name ON Customers(Name);
```

## 14. Function vs Stored Procedure?
- **Function**: Returns computed values, no table modifications.  
- **Stored Procedure**: Mini-programs, can modify tables.  
**Example**:  
```sql
CREATE FUNCTION GetTotal(@id INT) RETURNS INT AS
BEGIN
  RETURN (SELECT SUM(Amount) FROM Orders WHERE CustomerID = @id);
END;
```

## 15. What are triggers and why do you need them?
Triggers execute logic on events (INSERT/UPDATE/DELETE) for automation.  
**Example**: Log changes to `Orders`.  
```sql
CREATE TRIGGER LogOrderUpdate
ON Orders
AFTER UPDATE
AS
INSERT INTO AuditLog (OrderID, ChangeDate) VALUES (inserted.OrderID, GETDATE());
```

## 16. What are types of triggers?
AFTER and INSTEAD OF.

## 17. Differentiate between After Trigger vs Instead Of Trigger?
- **AFTER**: Runs after the event.  
- **INSTEAD OF**: Replaces the event.  
**Example**:  
```sql
CREATE TRIGGER InsteadOfDelete
ON Orders
INSTEAD OF DELETE
AS
UPDATE Orders SET IsDeleted = 1 WHERE OrderID = deleted.OrderID;
```

## 18. What is the need for Identity?
Auto-incrementing column for unique IDs.  
**Example**:  
```sql
CREATE TABLE Products (ProductID INT IDENTITY(1,1) PRIMARY KEY, Name VARCHAR(50));
```

## 19. Explain transactions and how to implement them?
Treats operations as a single unit (all succeed or fail).  
**Example**:  
```sql
BEGIN TRANSACTION;
UPDATE Accounts SET Balance = Balance - 100 WHERE ID = 1;
UPDATE Accounts SET Balance = Balance + 100 WHERE ID = 2;
COMMIT;
```

## 20. What are Inner Joins?
Select matching records from both tables.  
**Example**:  
```sql
SELECT c.Name, o.OrderID
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID;
```

## 21. Explain Left Join?
All records from left table, matching from right.  
**Example**:  
```sql
SELECT c.Name, o.OrderID
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID;
```

## 22. Explain Right Join?
All records from right table, matching from left.  
**Example**:  
```sql
SELECT c.Name, o.OrderID
FROM Customers c
RIGHT JOIN Orders o ON c.CustomerID = o.CustomerID;
```

## 23. Explain Full Outer Join?
All records from both tables, matching or not.  
**Example**:  
```sql
SELECT c.Name, o.OrderID
FROM Customers c
FULL OUTER JOIN Orders o ON c.CustomerID = o.CustomerID;
```

## 24. Explain Cross Join?
Cartesian product of all records.  
**Example**:  
```sql
SELECT c.Name, p.ProductName
FROM Customers c
CROSS JOIN Products p;
```

## 25. Why do we need UNION?
Combines result sets from multiple queries.  
**Example**:  
```sql
SELECT ProductID, Name FROM Products
UNION
SELECT ProductID, Name FROM ExpiredProducts;
```

## 26. Differentiate between Union vs Union All?
- **Union**: Removes duplicates.  
- **Union All**: Includes duplicates, faster.  
**Example**:  
```sql
SELECT Name FROM Products
UNION ALL
SELECT Name FROM ExpiredProducts;
```

## 27. Can we have unequal columns in Union?
No, column count must match.  
**Example (Invalid)**:  
```sql
SELECT Name FROM Products
UNION ALL
SELECT ProductID, Name FROM ExpiredProducts;  -- Error
```

## 28. Can columns have different data types in Union?
No, data types must be compatible.  
**Example (Invalid)**:  
```sql
SELECT Name, ProductID FROM Products
UNION ALL
SELECT ProductID, Name FROM ExpiredProducts;  -- Error
```

## 29. Which aggregate functions have you used?
SUM, AVG, MAX, MIN, COUNT.  
**Example**:  
```sql
SELECT SUM(Amount), AVG(Amount), MIN(Amount), MAX(Amount), COUNT(*)
FROM Orders;
```

## 30. When to use Group By?
To summarize rows based on common values.  
**Example**:  
```sql
SELECT ProductName, SUM(Amount)
FROM Orders
GROUP BY ProductName;
```

## 31. Can we select columns not part of Group By?
No, non-grouped columns must be aggregated.  
**Example (Invalid)**:  
```sql
SELECT CustomerName, ProductName, SUM(Amount)
FROM Orders
GROUP BY ProductName;  -- Error
```

## 32. What is the Having clause?
Filters grouped data.  
**Example**:  
```sql
SELECT ProductName, SUM(Amount) AS Total
FROM Orders
GROUP BY ProductName
HAVING SUM(Amount) > 1000;
```

## 33. Having clause vs Where clause?
- **Having**: Filters groups, uses aggregates, applied after GROUP BY.  
- **Where**: Filters rows, no aggregates, applied before GROUP BY.  
**Example**:  
```sql
SELECT ProductName, SUM(Amount)
FROM Orders
WHERE Amount > 100
GROUP BY ProductName
HAVING SUM(Amount) > 1000;
```

## 34. How can we sort records?
Use ORDER BY.  
**Example**:  
```sql
SELECT * FROM Orders
ORDER BY Amount DESC;
```

## 35. What’s the default sort?
Ascending (ASC).

## 36. How can we remove duplicates?
Use DISTINCT.  
**Example**:  
```sql
SELECT DISTINCT ProductName FROM Orders;
```

## 37. Select the first top X records?
Use TOP.  
**Example**:  
```sql
SELECT TOP 2 * FROM Orders;
```

## 38. How to handle NULLs?
Use ISNULL to replace NULLs.  
**Example**:  
```sql
SELECT ISNULL(Name, 'Unknown') FROM Customers;
```

## 39. What is the use of wildcards?
Pattern matching with LIKE.  
**Example**:  
```sql
SELECT * FROM Customers WHERE Name LIKE 'S%';
```

## 40. What is the use of Alias?
Renames columns/tables for clarity.  
**Example**:  
```sql
SELECT CustomerName AS Name, Amount AS Total
FROM Orders;
```

## 41. How to write a Case statement?
Conditional logic in queries.  
**Example**:  
```sql
SELECT CustomerName,
  CASE
    WHEN Amount < 200 THEN 'Low'
    WHEN Amount > 200 THEN 'High'
    ELSE 'Medium'
  END AS Category
FROM Orders;
```

## 42. What are self-referential tables?
Tables with a foreign key referencing their own primary key.  
**Example**: Employee table with `ManagerID` referencing `EmployeeID`.

## 43. What is a self-join?
Joining a table with itself.  
**Example**:  
```sql
SELECT e1.Name, e2.Name AS Manager
FROM Employees e1
INNER JOIN Employees e2 ON e1.ManagerID = e2.EmployeeID;
```

## 44. Explain the Between clause?
Selects values in a range.  
**Example**:  
```sql
SELECT * FROM Orders
WHERE Amount BETWEEN 100 AND 200;
```

## 45. Explain Subquery?
A query inside another query.  
**Example**:  
```sql
SELECT Name
FROM Customers
WHERE CustomerID IN (SELECT CustomerID FROM Orders WHERE Amount > 1000);
```

## 46. Can inner Subquery return multiple results?
Yes, with IN operator.  
**Example**:  
```sql
SELECT Name
FROM Customers
WHERE CustomerID IN (SELECT CustomerID FROM Orders);
```

## 47. What is a Correlated Query?
Outer query sends rows to inner query for evaluation.  
**Example**:  
```sql
SELECT Name
FROM Customers c
WHERE EXISTS (SELECT 1 FROM Orders o WHERE o.CustomerID = c.CustomerID);
```

## 48. Differentiate between Joins and Subquery?
- **Join**: Combines tables for matching/non-matching rows.  
- **Subquery**: Nested processing, inner query feeds outer query.  
**Example**: Join is often clearer for combining tables; subqueries for complex logic.

## 49. Performance: Joins vs Subquery?
Joins are usually faster, but subqueries can be optimized for specific cases. Check query plan.

## 50. Select the top nth highest salary using TOP and ORDER BY?
**Example**:  
```sql
SELECT TOP 1 Salary
FROM (SELECT DISTINCT TOP 3 Salary FROM Employees ORDER BY Salary DESC) AS Sub
ORDER BY Salary ASC;
```

## 51. Select the top nth highest salary using Correlated Queries?
**Example**:  
```sql
SELECT Salary
FROM Employees e1
WHERE 2 = (SELECT COUNT(DISTINCT Salary) FROM Employees e2 WHERE e2.Salary > e1.Salary);
```

## 52. Select top nth using T-SQL?
Use ROW_NUMBER().  
**Example**:  
```sql
SELECT Salary
FROM (SELECT Salary, ROW_NUMBER() OVER (ORDER BY Salary DESC) AS rn FROM Employees) AS Ranked
WHERE rn = 3;
```

## 53. Performance comparison of all methods for top nth salary?
- **TOP**: Simple, good for small datasets.  
- **Correlated**: Slower for large datasets due to row-by-row evaluation.  
- **ROW_NUMBER**: Efficient, flexible for ranking. Check query plan for specifics.

## 54. What is a CTE?
Common Table Expression: Temporary result set for a single query scope.  
**Example**:  
```sql
WITH TopCustomers AS (SELECT CustomerID, SUM(Amount) AS Total FROM Orders GROUP BY CustomerID)
SELECT c.Name, t.Total
FROM Customers c
JOIN TopCustomers t ON c.CustomerID = t.CustomerID;
```

## 55. Can we execute CTE multiple times?
Yes, within the same query scope.  
**Example**: Reference `TopCustomers` multiple times in joins.

## 56. What is the use of CTE?
Simplifies complex queries, supports recursion.  
**Example**: Hierarchical data like employee-manager relationships.

## 57. How to write a recursive CTE?
**Example**:  
```sql
WITH Numbers AS (
  SELECT 1 AS n
  UNION ALL
  SELECT n + 1 FROM Numbers WHERE n < 3
)
SELECT * FROM Numbers;  -- Returns 1, 2, 3
```

## 58. Can we see some real-world examples of CTE?
Hierarchical data (e.g., org chart), recursive path traversal.  
**Example**:  
```sql
WITH EmpHierarchy AS (
  SELECT EmployeeID, Name, ManagerID, 1 AS Level
  FROM Employees WHERE ManagerID IS NULL
  UNION ALL
  SELECT e.EmployeeID, e.Name, e.ManagerID, h.Level + 1
  FROM Employees e
  JOIN EmpHierarchy h ON e.ManagerID = h.EmployeeID
)
SELECT * FROM EmpHierarchy;
```

## 59. Can we perform insert/updates on CTEs?
No, CTEs are read-only.

## 60. Does CTE update the tables physically?
No, CTEs are temporary and don’t affect base tables.

## 61. What are temporary tables?
Physical tables for a session, dropped when session ends.  
**Example**:  
```sql
CREATE TABLE #TempProducts (ProductName VARCHAR(50), Price DECIMAL(10,2));
INSERT INTO #TempProducts VALUES ('Shoes', 100.23);
SELECT * FROM #TempProducts;
```

## 62. Temp tables vs CTE?
- **Temp Tables**: Physical, support indexes, session-scoped.  
- **CTE**: In-memory, query-scoped, no indexes.

## 63. Performance: CTE vs Temp tables?
Temp tables are faster for large data due to indexing; CTEs are simpler for small, recursive queries.

## 64. What are window functions in SQL?
Functions that operate on a set of rows (window) for calculations.  
**Example**:  
```sql
SELECT Name, ROW_NUMBER() OVER (ORDER BY Name) AS ID
FROM Customers;
```

## 65. What does the PARTITION BY clause do in window functions?
Divides rows into groups for function application.  
**Example**:  
```sql
SELECT Name, ROW_NUMBER() OVER (PARTITION BY Region ORDER BY Name) AS RegionRank
FROM Customers;
```

## 66. Is PARTITION BY similar to GROUP BY?
- **GROUP BY**: Aggregates rows into one per group.  
- **PARTITION BY**: Keeps all rows, applies function per group.

## 67. What is the difference between RANK vs DENSE_RANK?
- **RANK**: Skips ranks after ties.  
- **DENSE_RANK**: Consecutive ranks.  
**Example**:  
```sql
SELECT Name, Salary, RANK() OVER (ORDER BY Salary DESC) AS Rank,
DENSE_RANK() OVER (ORDER BY Salary DESC) AS DenseRank
FROM Employees;
-- Salary 100: Rank=1, DenseRank=1
-- Salary 100: Rank=1, DenseRank=1
-- Salary 90:  Rank=3, DenseRank=2
```

## 68. Find duplicate records from a table?
**Example**:  
```sql
SELECT Name, COUNT(*) AS Count
FROM Customers
GROUP BY Name
HAVING COUNT(*) > 1;
```

## 69. Find unique records?
**Example**:  
```sql
SELECT Name, COUNT(*) AS Count
FROM Customers
GROUP BY Name
HAVING COUNT(*) = 1;
```

## 70. Delete duplicate records (with ID)?
**Example**:  
```sql
DELETE FROM Customers
WHERE CustomerID NOT IN (
  SELECT MIN(CustomerID)
  FROM Customers
  GROUP BY Name
);
```

## 71. Delete duplicate records (without ID)?
**Example**:  
```sql
DELETE FROM (
  SELECT ROW_NUMBER() OVER (PARTITION BY Name ORDER BY Name) AS rn
  FROM Customers
) AS t
WHERE rn > 1;
```

## 72. Delete duplicate records (using CTE)?
**Example**:  
```sql
WITH RankedNames AS (
  SELECT Name, ROW_NUMBER() OVER (PARTITION BY Name ORDER BY Name) AS rn
  FROM Customers
)
DELETE FROM RankedNames WHERE rn > 1;
```