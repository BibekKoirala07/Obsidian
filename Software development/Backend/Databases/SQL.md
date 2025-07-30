### Basic SQL
Those are things like Data Retrieval, Filtering, Basic Aggregation and Grouping Data. 
###### Data Retrieval
###### Filtering
###### Basic Aggregation
###### Grouping Data

### Joins
### Normalization
### Window Function
### Indexing & Performance
### Sub queries

### Basic SQL
Things like ==Data Retrieval==, ==Filtering== , ==Basic Aggregation== and ==Grouping data==. An Aggregate function is a function that performs a calculation on a set of values, and returns a single value. Some of the aggregate functions are `MAX()`, `MIN()`, `COUNT()`, `SUM()`, `AVG()` and other depending on database. It ignores NULL values except `Count()`. It is evaluated after `WHERE` clause. When we use aggregate function, we just get one result and that column name will be not descriptive and random. To solve that we use Alias. It is used as `AS`. We can put multiple column in one column and give it different name through aliasing. Like we have different fields for postal code, city, state and we need all column in same. we can achieve through aliasing.

We can also use limit but it is different for different database. Like Limit is used my MySQL, FETCH FIRST by ORACLE and so on. 

There is UNION(it return all unique value) and UNION ALL(return all value) also. It works as to combine two or more than two result of `SELECT`.
```sql
SELECT City, Country FROM Customers  
WHERE Country='Germany'  
UNION ALL -- It gives all the result while Just UNION gives unique only results. 
SELECT City, Country FROM Suppliers  
WHERE Country='Germany'  
ORDER BY City;
```

For grouping data, GROUP BY is used. Like lets say we have customer from different city, some may live in same city. What if we want to number of customers that live in one city ? Or we want to see how many people work at one branch ? `GROUP BY` is used. However, GROUP BY doesn't work for all. Like we need something in select for it to work. We can't use * in `GROUP BY`. Now, after the rows has been calculated using `GROUP BY`, we need to use `HAVING` as `WHERE` doesn't work after the operation is done. Remember, it is not that `WHERE` is not usable but it has already been applied and and now we need to do filtering after all previous operation ? Like we need to display only cities which have more than certain customer ? Then `HAVING` is used. `WHERE` can't be used as it has already been applied before `GROUP BY`.  

For Operators There are Lot of types included. Like there is Arithmetic Operators like addition, sub, mul, div, and modulo also. Comparison Operators, just like name greater than, less than, not equal to and other things. Also Bitwise Operators like &, | and ^ (it is exclusive OR). And the most important operators like AND, OR, EXIST and other things.

There are other things like `ANY`, `OR`, `ALL` which is better to learn at the time of use rather than listing here.
``` sql
-- Retrieve data with filtering, sorting, and pagination
SELECT DISTINCT column
-- with distinct we get distinct value. other wise lets say we have 500 people from 200 countries. So there will be 500 rows for each people. if no distinct is given, then no of row in result will be same as table but if we use distinct, there will be only 200 rows, it works for whatever column Name is typed.
FROM table
WHERE age BETWEEN 20 AND 30
  AND city NOT IN ('Los Angeles', 'Chicago') -- NOT can be added anywhere, It is seperate with IN and not same NOT and IN are different
  AND name ILIKE 'J%' -- ILIKE is case-insensitive, while LIKE is case-sensitive
  AND created_at >= '2023-01-01'
  AND name IS NULL -- IS NOT NULL
ORDER BY created_at DESC, age ASC -- first created_at and if same then only age
ORDER BY name, name DESC  -- we can sort using name and also descending order also
ORDER BY name, address  -- first name, if same name only addres
LIMIT 5 OFFSET 5

-- Group, aggregate, and filter aggregated results
SELECT city, COUNT(*) AS user_count, AVG(age) AS avg_age, MAX(age) AS oldest_age
FROM users
WHERE created_at >= '2023-01-01'
GROUP BY city
HAVING COUNT(*) > 10 AND AVG(age) < 35 -- HAVING filters groups, not rows
ORDER BY user_count DESC -- Sort groups by user count in descending order
-- HAVING is necessary for filtering aggregated values; WHERE cannot be used here.
-- Common pitfall: forgetting GROUP BY when using aggregate functions like COUNT or AVG.
```
### Joins
Used to combine rows from two or more tables. There are many types of Joins. However mainly there are two types. They are Inner join and Outer Joins. Let's say there are two tables. In Inner Joins. It returns row where there is a match on both tables. It filter out not unmatched rows. For Outer Joins, There are Left, Right and Full Outer joins. In Left Join, it return all the column for left table and matching records from right table. Same but Vice versa for Right Join. And there are Cross Join and Self Join also.
![[Pasted image 20241216153417.png]]
3. **Cross Join**
    
    - Cartesian product of two tables.
4. **Self Join**
    
    - Joining a table with itself.

### Data Manipulation
Some of the operation are Insert, Update and Delete.
```sql
INSERT INTO users (name, email, city, age, created_at) 
VALUES 
('John Doe', 'john@example.com', 'New York', 25, CURRENT_TIMESTAMP),
('Jane doe', 'jane@example.com', 'Pokhaa', 25, CURRENT_TIMESTAMP);
-- we don't need to specify (name, email ...) if all the column in single row is to be filled. and we can inset multiple row at same time

UPDATE users
SET city = 'San Francisco', age = 26 -- Update specific columns
WHERE email = 'john@example.com'; -- Always use WHERE to avoid updating all rows
-- Common pitfall: accidentally updating the entire table without a WHERE clause

DELETE FROM users
WHERE age < 18;
-- This means that the table structure, attributes, and indexes will be intact which is different from TRUNCATE
-- Common pitfall: omitting WHERE, which deletes all rows

DROP TABLE tablename; -- delete table

-- Remove all rows but keep the table structure
TRUNCATE TABLE users; 
-- Faster than DELETE for large datasets but cannot be rolled back if not in a transaction
-- TRUNCATE resets identity columns in most databases (e.g., AUTO_INCREMENT in MySQL)
```
### Schema and Table Management
We can `CREATE` database. But make sure to create with Admin privilege so that we can do anything. Same with `DROP` it. So do it carefully. We can backup to disk also. There is a option of differential also. Meaning it will only back up data that have been changed from last. There is create table features also.
While creating table we can include constraints like 
`NOT NULL`, 
`UNIQUE`, 
`PRIMARY KEY`, it is combination of `NOT NULL` and `UNIQUE` . There is only one PK in one table. The thing is that we can use two column as primary key. Like there is id and roll no, we can use combination of two value as primary key.
`FOREIGHN KEY`, it refers to primary key in another table. The table with Primary key is called parent table and table with FK is called child table.
`CHECK`, `DEFAULT`,
`CREATE INDEX`
There is also feature to `DROP` the table. Also to `TRUNCATE` the table. `Drop` will delete the table while `TRUNCATE` will only delete the data and not table. We can also use `ALTER` the database like adding column, delete column, modify and it is also used to change the data type of required columns.

Indexing is also available and it speeds up searching however it requires more time to update. So only put index on column that will be frequently searched against or will be searched.

For Data Types look for them in internet as every database have different data types. Some are same like Int, Date, Varchar and many more.

==Do Read about Date format before doing anything== . As it is extremely hard to memorize and I will not write here. And don't use Time component unless you have to. It is not worth it as it introduce next level of bugs.
```sql
CREATE TABLE ComprehensiveOrders (
    OrderID INT NOT NULL AUTO_INCREMENT, -- Auto-incremented unique identifier
    CustomerID INT NOT NULL, -- Foreign key to Customers table
    ProductID INT DEFAULT NULL, -- Optional product reference
    OrderDate DATETIME DEFAULT CURRENT_TIMESTAMP, -- Auto-sets to the current timestamp
    DeliveryDate DATE, -- Expected delivery date
    OrderAmount DECIMAL(12, 2) CHECK (OrderAmount >= 0), -- Total with validation for non-negative values
    PaymentStatus VARCHAR(20) DEFAULT 'Pending', -- Status with default
    IsPaid BOOLEAN DEFAULT FALSE, -- Payment status, true/false
    PaymentMethod ENUM('Cash', 'Card', 'Online', 'Other') DEFAULT 'Cash', -- Restricts input to predefined methods
    Notes TEXT, -- Additional order details
    DiscountCode CHAR(10), -- Fixed-length discount code
    OrderPriority TINYINT UNSIGNED DEFAULT 0, -- Priority levels (0 = Normal, 1 = High)
    TotalWeight FLOAT DEFAULT 0.0, -- Approximate total weight of items
    TaxRate DOUBLE(5, 3) CHECK (TaxRate BETWEEN 0 AND 1), -- Percentage range validation for tax
    ShipmentID BINARY(16), -- Unique binary identifier, e.g., UUID
    ProductData JSON, -- Flexible structure for product metadata
    OrderStatus SET('Pending', 'Processing', 'Shipped', 'Delivered', 'Cancelled'), -- Multiple selectable states
    PRIMARY KEY (OrderID, CustomerID), -- Composite primary key using two columns
    UNIQUE (DiscountCode), -- Ensures unique discount codes
    INDEX idx_OrderDate (OrderDate), -- Improves search performance on OrderDate
    FULLTEXT INDEX ft_Notes (Notes), -- Enables full-text search for Notes
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE, -- Deletes or updates linked customer rows automatically
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID) 
        ON DELETE SET NULL, -- Nullifies ProductID if related product is deleted
    CONSTRAINT chk_OrderAmount CHECK (OrderAmount >= 0), -- Named constraint for validation
    CONSTRAINT fk_ShipmentID FOREIGN KEY (ShipmentID) REFERENCES Shipments(ShipmentID) -- Explicit foreign key
) ENGINE=InnoDB -- InnoDB supports transactions and foreign keys
AUTO_INCREMENT=1001 -- Start ID generation from 1001
DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci -- Multilingual support with case-insensitive collation
COMMENT = 'Comprehensive table showcasing SQL table options';
```
### **Indexes**

1. **Purpose of Indexes**
    
    - Improve query performance.
2. **Types of Indexes**
    
    - Single-column index.
    - Composite index (multiple columns).
3. **Creating and Dropping Indexes**
    
    - `CREATE INDEX`, `DROP INDEX`.

---

### Advanced SQL
We can copy data from one table to another using `SELECT INTO`. That new table will be created. However if new table is already present, then we can use `INSERT INTO SELECT`. Data source type and target should match. Let's say we want to get if else in query like below one then `CASE` is used.
```sql
SELECT CustomerName, City, Country  
FROM Customers  
ORDER BY  
(CASE  
    WHEN City IS NULL THEN Country  
    ELSE City  
END);
```
Then, after that, we can also use SQL function like `IFNULL`, `COALENSE` to check for the value is NULL or not and if it is NULL, then return something else. Like in below thing. No need to rote it as different database has different syntax to achieve it.
```sql
	SELECT ProductName, UnitPrice * (UnitsInStock + IFNULL(UnitsOnOrder, 0))  
	FROM Products;
```
And there is also function like thing in SQL called stored procedures. and we can send parameters too 😍😍😍.

1. **Subqueries**
    - Single-row subqueries.
    - Multi-row subqueries (`IN`, `ANY`, `ALL`).
    - Correlated subqueries.
2. **Common Table Expressions (CTEs)**
    
    - `WITH` clause for temporary result sets.
3. **Window Functions**
    
    - `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`.
    - Aggregate functions with `OVER` clause (e.g., `SUM`, `AVG`).
4. **Set Operations**
    
    - `UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`.
5. **Recursive Queries**
    
    - Recursive `WITH` queries.

---

### **Performance Optimization**

1. **Execution Plans**
    
    - Understand how queries are executed.
2. **Query Optimization**
    
    - Index usage, reducing nested subqueries, avoiding `SELECT *`.
3. **Normalization and Denormalization**
    
    - Understand normalization forms (1NF, 2NF, 3NF).
4. **Partitioning**
    
    - Horizontal and vertical partitioning.

---

### **Database Administration**

1. **Transactions**
    
    - `BEGIN`, `COMMIT`, `ROLLBACK`.
2. **ACID Properties**
    
    - Atomicity, Consistency, Isolation, Durability.
3. **Views**
    
    - Create reusable query logic.
4. **Stored Procedures**
    
    - Encapsulate business logic in the database.
5. **Triggers**
    
    - Automate actions based on table events.

---

### **Practical Skills**

1. **Designing Database Schemas**
    
    - ER diagrams, relationships, normalization.
2. **Backup and Restore**
    
    - Understand `BACKUP` and `RESTORE` mechanisms.
3. **Database Security**
    
    - User roles, permissions, and `GRANT/REVOKE`.
4. **Using SQL with Programming Languages**
    
    - Interact with databases using Node.js (`pg` or `mysql2`), Python (`sqlalchemy`), etc.