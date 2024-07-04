Let's clarify what kind of procedures and functions you need for your MariaDB database. Based on your provided tables, here are a few potential procedures and functions you might find useful:

1. **Procedure to log job changes**: A stored procedure that inserts a new record into the `Employee_Log` table whenever an employee's job changes.

2. **Function to get current job**: A function that returns the current job of an employee given their employee number.

### 1. Procedure to Log Job Changes

This procedure will log the old job and new job for an employee whenever their job changes in the `Employee_Records` table.

```sql
DELIMITER //

CREATE PROCEDURE LogJobChange(IN p_Eno VARCHAR(10), IN p_NewJobName VARCHAR(50))
BEGIN
    DECLARE v_OldJobName VARCHAR(50);
    
    -- Get the current job name
    SELECT Job_Name INTO v_OldJobName
    FROM Employee_Records
    WHERE Eno = p_Eno;

    -- Check if the job has changed
    IF v_OldJobName != p_NewJobName THEN
        -- Insert a log entry
        INSERT INTO Employee_Log (Eno, Ename, Old_Job_Name, New_Job_Name, Update_Time)
        SELECT Eno, Ename, v_OldJobName, p_NewJobName, NOW()
        FROM Employee_Records
        WHERE Eno = p_Eno;

        -- Update the job in the Employee_Records table
        UPDATE Employee_Records
        SET Job_Name = p_NewJobName
        WHERE Eno = p_Eno;
    END IF;
END //

DELIMITER ;
```

### 2. Function to Get Current Job

This function will return the current job of an employee given their employee number.

```sql
DELIMITER //

CREATE FUNCTION GetCurrentJob(p_Eno VARCHAR(10))
RETURNS VARCHAR(50)
DETERMINISTIC
BEGIN
    DECLARE v_JobName VARCHAR(50);

    -- Get the current job name
    SELECT Job_Name INTO v_JobName
    FROM Employee_Records
    WHERE Eno = p_Eno;

    RETURN v_JobName;
END //

DELIMITER ;
```

### Usage Examples

To log a job change:

```sql
CALL LogJobChange('E0033', 'Finance Manager');
```

To get the current job of an employee:

```sql
SELECT GetCurrentJob('E0033');
```

These examples should give you a good starting point. Adjust the procedures and functions according to your specific requirements. Let me know if you need further assistance!
