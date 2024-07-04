Rollback and commit operations are fundamental to managing transactions in a relational database. They ensure data integrity by allowing transactions to be completed successfully or undone entirely in case of an error. Let's look at these operations in detail with examples.

### Commit Operation

The `COMMIT` operation is used to save all the changes made during a transaction. Once a transaction is committed, the changes become permanent and visible to other transactions.

#### Example

```sql
-- Start a new transaction
START TRANSACTION;

-- Step 1: Insert a new record
INSERT INTO Employee_Records (Eno, Ename, Gender, Job_Name, Department)
VALUES ('E0077', 'Alice Johnson', 'Female', 'Project Manager', 'IT');

-- Step 2: Update an existing record
UPDATE Employee_Records SET Job_Name = 'Senior Developer' WHERE Eno = 'E0022';

-- Step 3: Delete a record
DELETE FROM Employee_Records WHERE Eno = 'E0044';

-- Commit the transaction
COMMIT;
```

### Rollback Operation

The `ROLLBACK` operation is used to undo all changes made during a transaction. This is useful when an error occurs, and you want to revert the database to its previous state.

#### Example

```sql
-- Start a new transaction
START TRANSACTION;

-- Step 1: Insert a new record
INSERT INTO Employee_Records (Eno, Ename, Gender, Job_Name, Department)
VALUES ('E0077', 'Alice Johnson', 'Female', 'Project Manager', 'IT');

-- Step 2: Update an existing record
UPDATE Employee_Records SET Job_Name = 'Senior Developer' WHERE Eno = 'E0022';

-- An error occurs here, and we decide to rollback the transaction
ROLLBACK;
```

### Complete Example with Savepoint, Commit, and Rollback

Combining `SAVEPOINT`, `ROLLBACK TO SAVEPOINT`, `COMMIT`, and `ROLLBACK` for complex transaction management.

```sql
-- Start the transaction
START TRANSACTION;

-- Step 1: Insert a new record
INSERT INTO Employee_Records (Eno, Ename, Gender, Job_Name, Department)
VALUES ('E0077', 'Alice Johnson', 'Female', 'Project Manager', 'IT');

-- Create a savepoint
SAVEPOINT savepoint1;

-- Step 2: Update an existing record
UPDATE Employee_Records SET Job_Name = 'Senior Developer' WHERE Eno = 'E0022';

-- Another savepoint
SAVEPOINT savepoint2;

-- Step 3: Delete a record
DELETE FROM Employee_Records WHERE Eno = 'E0044';

-- An error occurs after this point, decide to rollback to the second savepoint
ROLLBACK TO SAVEPOINT savepoint2;

-- Step 4: Another update after rollback
UPDATE Employee_Records SET Job_Name = 'Lead Developer' WHERE Eno = 'E0022';

-- Commit the transaction
COMMIT;
```

### Explanation

1. **Start the Transaction**: Begin a new transaction.
2. **Insert a Record**: Add a new employee.
3. **Create Savepoint**: Set a savepoint named `savepoint1`.
4. **Update a Record**: Change the job name for `Eno = 'E0022'`.
5. **Another Savepoint**: Set another savepoint named `savepoint2`.
6. **Delete a Record**: Remove the employee with `Eno = 'E0044'`.
7. **Rollback to Savepoint**: Revert changes back to `savepoint2`, undoing the delete operation.
8. **Another Update**: Update the job name again after the rollback.
9. **Commit Transaction**: Save all changes made since the start of the transaction.

### Summary

- **COMMIT**: Saves all changes made during the transaction and makes them permanent.
- **ROLLBACK**: Undoes all changes made during the transaction, reverting the database to its state before the transaction began.
- **SAVEPOINT**: Sets a point within a transaction to which you can later roll back.
- **ROLLBACK TO SAVEPOINT**: Reverts the transaction to the state at the specified savepoint, undoing all changes made after that savepoint.

Using these operations effectively helps manage transaction flow, ensuring data consistency and integrity, especially in complex scenarios.
