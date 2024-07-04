Deadlocks occur when two or more transactions are waiting for each other to release locks, causing a cycle of dependencies that cannot be resolved. Here’s an example scenario using your tables, where two transactions try to update records in a way that can lead to a deadlock.

### Example Setup

We'll use two transactions that attempt to update different rows in the `Employee_Records` table but access the rows in different orders.

#### 1. Initial Data

Ensure your `Employee_Records` table has some data:

```sql
SELECT * FROM Employee_Records;
```

Let's assume it returns:

```plaintext
+-------+------------------+--------+------------------------------+-----------------+
| Eno   | Ename            | Gender | Job_Name                     | Department      |
+-------+------------------+--------+------------------------------+-----------------+
| E0011 | Smith Tailor     | Male   | Sale Manager                 | Sales           |
| E0022 | James William    | Male   | Marketing Manager            | Marketing       |
| E0033 | John Smith       | Male   | Account Manager              | Finance         |
| E0044 | Micheal Williams | Male   | Information Services Manager | Administration  |
| E0055 | Kim Marshal      | Female | Account Manager              | Administration  |
| E0066 | Jane Doe         | Female | HR Manager                   | Human Resources |
+-------+------------------+--------+------------------------------+-----------------+
```

### Step-by-Step Deadlock Scenario

#### Transaction 1 (Session 1)

1. Start Transaction:

    ```sql
    START TRANSACTION;
    ```

2. Update the `Job_Name` for employee `E0033`:

    ```sql
    UPDATE Employee_Records
    SET Job_Name = 'Finance Manager'
    WHERE Eno = 'E0033';
    ```

3. Now, without committing, Transaction 1 attempts to update another row:

    ```sql
    UPDATE Employee_Records
    SET Job_Name = 'Senior Sales Manager'
    WHERE Eno = 'E0011';
    ```

    At this point, Transaction 1 holds a lock on the row with `Eno = 'E0033'` and is attempting to acquire a lock on the row with `Eno = 'E0011'`.

#### Transaction 2 (Session 2)

1. Start Transaction:

    ```sql
    START TRANSACTION;
    ```

2. Update the `Job_Name` for employee `E0011`:

    ```sql
    UPDATE Employee_Records
    SET Job_Name = 'Senior Sales Manager'
    WHERE Eno = 'E0011';
    ```

3. Now, without committing, Transaction 2 attempts to update the row locked by Transaction 1:

    ```sql
    UPDATE Employee_Records
    SET Job_Name = 'Finance Manager'
    WHERE Eno = 'E0033';
    ```

### Deadlock Detection

When Transaction 2 tries to acquire a lock on `Eno = 'E0033'`, it will be blocked because Transaction 1 holds the lock on that row. At the same time, Transaction 1 is blocked waiting for the lock on `Eno = 'E0011'`, held by Transaction 2. This circular waiting causes a deadlock.

### Handling Deadlocks

MariaDB detects deadlocks and will automatically terminate one of the transactions to resolve the deadlock. You can check for deadlocks by handling exceptions in your application logic.

### Example Deadlock Handling

Here’s how you might handle this in an application using pseudo-code:

```python
try:
    # Start Transaction 1
    cursor.execute("START TRANSACTION;")
    cursor.execute("UPDATE Employee_Records SET Job_Name = 'Finance Manager' WHERE Eno = 'E0033';")
    cursor.execute("UPDATE Employee_Records SET Job_Name = 'Senior Sales Manager' WHERE Eno = 'E0011';")
    cursor.execute("COMMIT;")
except Exception as e:
    if 'Deadlock found' in str(e):
        # Handle deadlock by retrying transaction
        retry_transaction()
    else:
        # Handle other exceptions
        handle_other_exceptions()
```

By using this approach, you can ensure that your application is resilient to deadlocks and can retry transactions if necessary. 

This example demonstrates a typical deadlock scenario and how to manage it effectively.
