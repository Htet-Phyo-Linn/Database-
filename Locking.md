Sure, let's explore how to use locking with simple queries to ensure data consistency and prevent concurrency issues. We'll use row-level locking in a transactional context.

### Example of Locking with Simple Queries

We'll demonstrate how to use row-level locking with the `FOR UPDATE` clause to prevent concurrent transactions from causing inconsistencies.

#### Initial State

```sql
SELECT * FROM Employee_Records WHERE Eno = 'E0033';
```

Assume the output is:

```plaintext
+-------+-----------+--------+---------------+-----------+
| Eno   | Ename     | Gender | Job_Name      | Department|
+-------+-----------+--------+---------------+-----------+
| E0033 | John Smith| Male   | Account Manager| Finance   |
+-------+-----------+--------+---------------+-----------+
```

### Scenario

We have two transactions, Transaction A and Transaction B, that need to update the job name of employee `E0033`.

#### Transaction A (Session 1)

1. Start Transaction:

    ```sql
    START TRANSACTION;
    ```

2. Select the employee row and lock it for update:

    ```sql
    SELECT * FROM Employee_Records WHERE Eno = 'E0033' FOR UPDATE;
    ```

    This locks the row, preventing other transactions from modifying it until Transaction A completes.

3. Update the `Job_Name`:

    ```sql
    UPDATE Employee_Records
    SET Job_Name = 'Finance Manager'
    WHERE Eno = 'E0033';
    ```

4. Do not commit yet (simulating a long-running transaction).

#### Transaction B (Session 2)

1. Start Transaction:

    ```sql
    START TRANSACTION;
    ```

2. Try to select the employee row and lock it for update:

    ```sql
    SELECT * FROM Employee_Records WHERE Eno = 'E0033' FOR UPDATE;
    ```

    This query will be blocked until Transaction A commits, as the row is already locked by Transaction A.

### Commit Transactions

- **Transaction A** commits:

    ```sql
    COMMIT;
    ```

- **Transaction B** then continues after Transaction A commits:

    ```sql
    SELECT * FROM Employee_Records WHERE Eno = 'E0033' FOR UPDATE;
    ```

    Output: The updated row with `Job_Name = 'Finance Manager'`

3. Update the `Job_Name`:

    ```sql
    UPDATE Employee_Records
    SET Job_Name = 'HR Manager'
    WHERE Eno = 'E0033';
    ```

4. Commit Transaction B:

    ```sql
    COMMIT;
    ```

### Final State

```sql
SELECT * FROM Employee_Records WHERE Eno = 'E0033';
```

Output:

```plaintext
+-------+-----------+--------+-----------+-----------+
| Eno   | Ename     | Gender | Job_Name  | Department|
+-------+-----------+--------+-----------+-----------+
| E0033 | John Smith| Male   | HR Manager| Finance   |
+-------+-----------+--------+-----------+-----------+
```

### Explanation

1. **Transaction A** starts and locks the row for `Eno = 'E0033'` using `FOR UPDATE`. This prevents other transactions from modifying the row until Transaction A completes.
2. **Transaction B** starts and attempts to lock the same row using `FOR UPDATE`. However, it is blocked because Transaction A holds the lock.
3. **Transaction A** updates the row and commits, releasing the lock.
4. **Transaction B** continues after Transaction A commits, locks the row, updates it, and then commits.

### Summary

Using the `FOR UPDATE` clause ensures that a row is locked for the duration of a transaction, preventing other transactions from modifying it concurrently. This method ensures data consistency and prevents concurrency issues like lost updates. By using explicit row-level locking, you can control the access to critical sections of your database operations, ensuring that transactions are executed in a consistent and controlled manner.
