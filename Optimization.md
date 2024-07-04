### Tr လုပ်ခိုင်းတဲ့ထဲမပါ

Database optimization generally refers to improving the performance and efficiency of database operations. There are several types of optimizations that can be applied to database systems. Let's discuss some common types of optimizations and illustrate them with simple query examples.

### Types of Database Optimization

1. **Query Optimization**
2. **Indexing**
3. **Normalization**
4. **Denormalization**
5. **Partitioning**
6. **Materialized Views**
7. **Use of Stored Procedures**

Let's go through each type with a simple query example where applicable.

### 1. Query Optimization

Query optimization involves improving the efficiency of SQL queries by reorganizing their structure or using better algorithms for execution.

**Example:**
```sql
-- Before optimization
SELECT * FROM Employee_Records WHERE Department = 'IT' AND Gender = 'Female';

-- After optimization (assuming an index on (Department, Gender))
SELECT * FROM Employee_Records WHERE Gender = 'Female' AND Department = 'IT';
```

### 2. Indexing

Indexes improve the speed of data retrieval operations on a database table at the cost of additional storage space and decreased performance on inserts, updates, and deletes.

**Example:**
```sql
-- Create an index
CREATE INDEX idx_department ON Employee_Records(Department);

-- Query using the index
SELECT * FROM Employee_Records WHERE Department = 'IT';
```

### 3. Normalization

Normalization is the process of organizing data in a database to reduce redundancy and dependency. It divides larger tables into smaller tables and defines relationships between them.

**Example:**
```sql
-- Before normalization (single table)
CREATE TABLE Employees (
    Eno INT PRIMARY KEY,
    Ename VARCHAR(100),
    Gender VARCHAR(10),
    Job_Name VARCHAR(100),
    Department VARCHAR(100)
);

-- After normalization (split into two tables)
CREATE TABLE Employees (
    Eno INT PRIMARY KEY,
    Ename VARCHAR(100),
    Gender VARCHAR(10)
);

CREATE TABLE Jobs (
    Eno INT PRIMARY KEY,
    Job_Name VARCHAR(100),
    Department VARCHAR(100),
    FOREIGN KEY (Eno) REFERENCES Employees(Eno)
);
```

### 4. Denormalization

Denormalization involves adding redundant data to a normalized database to improve read performance by reducing the number of joins needed.

**Example:**
```sql
-- Denormalize for read performance
SELECT e.Eno, e.Ename, e.Gender, j.Job_Name
FROM Employees e
JOIN Jobs j ON e.Eno = j.Eno;
```

### 5. Partitioning

Partitioning involves splitting a large table into smaller, more manageable parts (partitions) to improve query performance and ease maintenance.

**Example:**
```sql
-- Partition by range
CREATE TABLE Sales (
    SaleId INT PRIMARY KEY,
    SaleDate DATE,
    Amount DECIMAL(10, 2),
    ...
) PARTITION BY RANGE (YEAR(SaleDate)) (
    PARTITION p1 VALUES LESS THAN (2020),
    PARTITION p2 VALUES LESS THAN (2021),
    PARTITION p3 VALUES LESS THAN (2022),
    ...
);
```

### 6. Materialized Views

Materialized views are precomputed views stored as a physical copy of the query results, which can improve query performance by reducing the need to recompute complex queries.

**Example:**
```sql
-- Create a materialized view
CREATE MATERIALIZED VIEW mv_employee_count_by_department AS
SELECT Department, COUNT(*) AS EmployeeCount
FROM Employees
GROUP BY Department;

-- Query the materialized view
SELECT * FROM mv_employee_count_by_department;
```

### 7. Use of Stored Procedures

Stored procedures are precompiled SQL queries stored in the database, which can reduce network traffic and improve performance by executing multiple SQL statements in one call.

**Example:**
```sql
-- Create a stored procedure
DELIMITER //
CREATE PROCEDURE GetEmployeeByDepartment(IN dept VARCHAR(100))
BEGIN
    SELECT * FROM Employees WHERE Department = dept;
END //
DELIMITER ;

-- Call the stored procedure
CALL GetEmployeeByDepartment('IT');
```

### Summary

Database optimization involves various techniques aimed at improving query performance, reducing redundancy, and enhancing overall efficiency. Each type of optimization serves different purposes and can be used in combination to achieve optimal performance tailored to specific database requirements.
