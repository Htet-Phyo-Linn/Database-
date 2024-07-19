To create and test the subscription expiry logic with a 10-second interval for `subscription_end` and a check that runs every 5 seconds, follow these detailed steps:

### Step-by-Step Instructions

1. **Create Database Schema:**

   ```sql
   CREATE TABLE IF NOT EXISTS users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(255),
       email VARCHAR(255),
       subscription_start DATETIME,
       subscription_end DATETIME,
       is_premium BOOLEAN DEFAULT FALSE,
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
   );
   ```

2. **Create Trigger to Update `is_premium`:**

   ```sql
   DELIMITER //

   CREATE TRIGGER update_is_premium BEFORE UPDATE ON users
   FOR EACH ROW
   BEGIN
       IF NEW.subscription_end < NOW() THEN
           SET NEW.is_premium = FALSE;
       ELSE
           SET NEW.is_premium = TRUE;
       END IF;
   END //

   DELIMITER ;
   ```

3. **Insert a User with a Subscription that Expires in 10 Seconds:**

   ```sql
   INSERT INTO users (name, email, subscription_start, subscription_end, is_premium, created_at, updated_at)
   VALUES ('John Doe', 'john@example.com', NOW(), DATE_ADD(NOW(), INTERVAL 10 SECOND), TRUE, NOW(), NOW());
   ```

4. **Create Stored Procedure to Check and Update Subscriptions Periodically:**

   ```sql
   DELIMITER //

   CREATE PROCEDURE update_all_subscriptions()
   BEGIN
       DECLARE done INT DEFAULT FALSE;
       DECLARE user_id INT;
       DECLARE cur CURSOR FOR SELECT id FROM users WHERE subscription_end < NOW() AND is_premium = TRUE;
       DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

       OPEN cur;
       read_loop: LOOP
           FETCH cur INTO user_id;
           IF done THEN
               LEAVE read_loop;
           END IF;

           UPDATE users
           SET is_premium = FALSE
           WHERE id = user_id;
       END LOOP;

       CLOSE cur;
   END //

   DELIMITER ;
   ```

5. **Schedule the Stored Procedure to Run Every 5 Seconds:**

   ```sql
   CREATE EVENT IF NOT EXISTS test_subscription_update
   ON SCHEDULE EVERY 5 SECOND
   DO
       CALL update_all_subscriptions();
   ```

6. **Enable Event Scheduler:**

   Ensure that the MySQL event scheduler is enabled. You can do this by running:

   ```sql
   SET GLOBAL event_scheduler = ON;
   ```

### Testing and Debugging

1. **Verify Event Scheduler Status:**

   Check if the event scheduler is running:

   ```sql
   SHOW VARIABLES LIKE 'event_scheduler';
   ```

   You should see a result like:

   ```
   +-----------------+-------+
   | Variable_name   | Value |
   +-----------------+-------+
   | event_scheduler | ON    |
   +-----------------+-------+
   ```

2. **Insert a Test User Again:**

   ```sql
   INSERT INTO users (name, email, subscription_start, subscription_end, is_premium, created_at, updated_at)
   VALUES ('John Doe', 'john@example.com', NOW(), DATE_ADD(NOW(), INTERVAL 10 SECOND), TRUE, NOW(), NOW());
   ```

3. **Monitor the User's Subscription Status:**

   After inserting the user, wait for at least 10 seconds and check the `is_premium` status:

   ```sql
   SELECT * FROM users WHERE email = 'john@example.com';
   ```

   You should see the `is_premium` field update to `FALSE` once the subscription expires.

### Debugging Tips

- You can change `10 SECOND` to `INTERVAL 1 MONTH`.
- Ensure the event scheduler is enabled.
- Verify that the event `test_subscription_update` exists and is scheduled to run every 5 seconds.
- Check the MySQL server logs for any errors related to the event or stored procedure.
- Ensure the trigger is correctly updating the `is_premium` field based on the `subscription_end` date.

By following these detailed steps, you should be able to test and verify the subscription expiry logic effectively.
