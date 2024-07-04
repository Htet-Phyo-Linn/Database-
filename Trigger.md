Sure, I'll show you how to create a trigger in MariaDB that automatically logs job changes into the `Employee_Log` table whenever an update is made to the `Job_Name` column in the `Employee_Records` table.

### Trigger to Log Job Changes

The trigger will execute before an update on the `Employee_Records` table. It will check if the `Job_Name` has changed and, if so, insert a record into the `Employee_Log` table.

Here's the SQL code to create such a trigger:

```sql
DELIMITER //

CREATE TRIGGER BeforeJobUpdate
BEFORE UPDATE ON Employee_Records
FOR EACH ROW
BEGIN
    DECLARE v_OldJobName VARCHAR(50);
    
    -- Get the old job name
    SET v_OldJobName = OLD.Job_Name;
    
    -- Check if the job name has changed
    IF v_OldJobName != NEW.Job_Name THEN
        -- Insert a log entry
        INSERT INTO Employee_Log (Eno, Ename, Old_Job_Name, New_Job_Name, Update_Time)
        VALUES (OLD.Eno, OLD.Ename, v_OldJobName, NEW.Job_Name, NOW());
    END IF;
END //

DELIMITER ;
```

### Explanation

1. **Trigger Timing and Event**:
   - `BEFORE UPDATE`: The trigger will activate before an update is executed on the `Employee_Records` table.

2. **Trigger Body**:
   - Declares a variable `v_OldJobName` to store the old job name.
   - Sets `v_OldJobName` to the current job name from the record that is being updated.
   - Compares the old job name (`v_OldJobName`) with the new job name (`NEW.Job_Name`). If they are different, it inserts a log entry into the `Employee_Log` table.

### Usage

Once the trigger is created, it will automatically log any changes to the `Job_Name` column in the `Employee_Records` table. You don't need to call it explicitly; it will fire whenever an update occurs.

For example, if you execute the following update statement:

```sql
UPDATE Employee_Records
SET Job_Name = 'Finance Manager'
WHERE Eno = 'E0033';
```

The trigger will automatically log this change in the `Employee_Log` table.

Feel free to adjust the trigger logic as needed to fit your specific requirements. Let me know if you need further customization or have additional questions!
