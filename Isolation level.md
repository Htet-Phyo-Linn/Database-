Isolation levels in database systems define the degree to which the operations in one transaction are isolated from those in other transactions. There are four main isolation levels, each offering different balances between consistency and concurrency:

1. **Read Uncommitted**
2. **Read Committed**
3. **Repeatable Read**
4. **Serializable**

Let's explore each isolation level with simple queries to illustrate their behavior.

### 1. Read Uncommitted

This isolation level allows transactions to read uncommitted changes made by other transactions, leading to possible dirty reads.

#### Example

**Transaction 1 (Session 1):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
START TRANSACTION;
UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';
-- Do not commit yet
```

**Transaction 2 (Session 2):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
START TRANSACTION;
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- This will see 'Finance Manager' even though Transaction 1 hasn't committed
COMMIT;
```

### 2. Read Committed

This isolation level prevents dirty reads by ensuring that any data read is committed at the moment it is read.

#### Example

**Transaction 1 (Session 1):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';
-- Do not commit yet
```

**Transaction 2 (Session 2):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- This will not see 'Finance Manager' because Transaction 1 hasn't committed
COMMIT;
```

### 3. Repeatable Read

This isolation level prevents dirty reads and non-repeatable reads. A transaction reads the same data if it is read multiple times during the transaction.

#### Example

**Transaction 1 (Session 1):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION;
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- Assume it returns 'Account Manager'
UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';
-- Do not commit yet
```

**Transaction 2 (Session 2):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION;
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- This will still see 'Account Manager' because Transaction 1 hasn't committed
COMMIT;
```

**Transaction 1 (Session 1):**
```sql
-- Continue from above
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- This will see 'Finance Manager'
COMMIT;
```

### 4. Serializable

This is the strictest isolation level, ensuring full serializability by preventing dirty reads, non-repeatable reads, and phantom reads.

#### Example

**Transaction 1 (Session 1):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
START TRANSACTION;
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- Assume it returns 'Account Manager'
UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';
-- Do not commit yet
```

**Transaction 2 (Session 2):**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
START TRANSACTION;
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- This will be blocked until Transaction 1 commits because of the strict serializability rules
COMMIT;
```

**Transaction 1 (Session 1):**
```sql
-- Continue from above
COMMIT;
```

**Transaction 2 (Session 2):**
```sql
-- Now it can proceed
SELECT Job_Name FROM Employee_Records WHERE Eno = 'E0033';
-- This will see 'Finance Manager'
COMMIT;
```

### Summary

Each isolation level provides different guarantees and trade-offs:

- **Read Uncommitted**: Allows dirty reads.
- **Read Committed**: Prevents dirty reads.
- **Repeatable Read**: Prevents dirty and non-repeatable reads.
- **Serializable**: Prevents dirty reads, non-repeatable reads, and phantom reads, ensuring full serializability.

Choosing the right isolation level depends on the specific needs of your application in terms of consistency and performance.
