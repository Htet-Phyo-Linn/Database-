Savepoints are intermediate points within a transaction that allow you to partially roll back a transaction to a specific point without rolling back the entire transaction. This feature is useful for handling errors in complex transactions. While savepoints themselves do not come in different "types," they can be used in various ways to control transaction flow.

### Using Savepoints

1. **Creating a Savepoint**
2. **Rolling Back to a Savepoint**
3. **Releasing a Savepoint**

Let's explore these with simple queries.

### 1. Creating a Savepoint

You can set a savepoint at any point during a transaction.

#### Example

```sql
START TRANSACTION;

-- Step 1: Update some data
UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';

-- Create a savepoint
SAVEPOINT savepoint1;
```

### 2. Rolling Back to a Savepoint

If you encounter an error or decide to undo changes made after a savepoint, you can roll back to that savepoint.

#### Example

```sql
-- Step 2: Update some more data
UPDATE Employee_Records SET Job_Name = 'HR Manager' WHERE Eno = 'E0055';

-- Suppose an error is encountered here, so we roll back to the savepoint
ROLLBACK TO SAVEPOINT savepoint1;
```

After rolling back to `savepoint1`, the changes made in Step 1 (`Job_Name = 'Finance Manager'` for `E0033`) are retained, but the changes made in Step 2 (`Job_Name = 'HR Manager'` for `E0055`) are undone.

### 3. Releasing a Savepoint

If you no longer need a savepoint, you can release it to free up resources.

#### Example

```sql
-- Continue with the transaction
UPDATE Employee_Records SET Job_Name = 'Chief Executive Officer' WHERE Eno = 'E0033';

-- Release the savepoint
RELEASE SAVEPOINT savepoint1;

-- Commit the transaction
COMMIT;
```

### Complete Example

Let's put everything together in a complete example.

```sql
-- Start the transaction
START TRANSACTION;

-- Step 1: Update some data
UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';

-- Create a savepoint
SAVEPOINT savepoint1;

-- Step 2: Update some more data
UPDATE Employee_Records SET Job_Name = 'HR Manager' WHERE Eno = 'E0055';

-- Suppose an error is encountered, so we roll back to the savepoint
ROLLBACK TO SAVEPOINT savepoint1;

-- Step 3: Update data again after rolling back
UPDATE Employee_Records SET Job_Name = 'Chief Executive Officer' WHERE Eno = 'E0033';

-- Release the savepoint
RELEASE SAVEPOINT savepoint1;

-- Commit the transaction
COMMIT;
```

### Explanation

1. **Start the Transaction**: Begin a new transaction.
2. **Update Data (Step 1)**: Change `Job_Name` for `Eno = 'E0033'` to `'Finance Manager'`.
3. **Create Savepoint**: Set a savepoint named `savepoint1`.
4. **Update Data (Step 2)**: Change `Job_Name` for `Eno = 'E0055'` to `'HR Manager'`.
5. **Rollback to Savepoint**: Undo the changes made after `savepoint1`, so the update in Step 2 is undone.
6. **Update Data (Step 3)**: Change `Job_Name` for `Eno = 'E0033'` to `'Chief Executive Officer'`.
7. **Release Savepoint**: Remove `savepoint1`.
8. **Commit Transaction**: Save all changes made since the start of the transaction.

### Summary

Savepoints allow finer control over transactions by enabling partial rollbacks. The key operations are creating savepoints, rolling back to savepoints, and releasing savepoints. Using these effectively can help manage complex transactions and handle errors gracefully.
