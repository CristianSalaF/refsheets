# PostgreSQL Quick Reference (Procedures, Functions, Triggers, and Cursors)

## 1. Common Variables & Error Codes
These are frequently needed in triggers, exceptions, and stored procedures.

## Declaration Types and Reference Types
- Variable declaration types
	- `attribute.type%` – References the type of a column from a table.
	- `rowtype` – Stores an entire row structure from a table.
	- `record` – Generic structure that can hold any row dynamically.
	- `TYPE` – Custom-defined composite type for structured data.

- **Special Variables in Triggers**
    - `NEW` → Contains the new row (INSERT/UPDATE).
    - `OLD` → Contains the old row (UPDATE/DELETE).
    - `TG_NAME` → Name of the trigger.
    - `TG_WHEN` → BEFORE or AFTER execution.
    - `TG_OP` → INSERT, UPDATE, DELETE, TRUNCATE operation.
    - `TG_TABLE_NAME` → Name of the table.

- **Common Exception Error Codes**
    - `no_data_found` → No rows found (`P0002`).
    - `too_many_rows` → More than one row returned (`P0003`).
    - `when others then` → Catch-all exception handler.

---

## 2. Transactions
Used for ensuring data consistency.

### **Basic Commands**
```sql
BEGIN;
-- SQL statements here
COMMIT;  -- Saves changes
ROLLBACK;  -- Undo changes
```

### **Using Savepoints**
```sql
SAVEPOINT savepoint_name;
ROLLBACK TO savepoint_name;  -- Rollback to a point
```

### **Example: Bank Transfer with Exception Handling**
```sql
DECLARE amount NUMBER := 100;
BEGIN
    UPDATE accounts SET balance = balance - amount WHERE id = 1;
    UPDATE accounts SET balance = balance + amount WHERE id = 2;
    COMMIT;
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        RAISE NOTICE 'Transaction error: %', SQLERRM;
END;
```

---

## 3. Exception Handling
Used for handling errors in stored procedures.

### **Basic Syntax**
```sql
BEGIN
    -- SQL statements
EXCEPTION
    WHEN no_data_found THEN
        RAISE EXCEPTION 'No record found!';
    WHEN too_many_rows THEN
        RAISE EXCEPTION 'Query returned too many rows!';
    WHEN OTHERS THEN
        RAISE EXCEPTION 'Unknown error: %', SQLERRM;
END;
```

---

## 4. Triggers
Used to automatically execute functions on table events.

### **Trigger Function Example**
```sql
CREATE OR REPLACE FUNCTION audit_salary_changes() RETURNS TRIGGER AS $$
BEGIN
    IF NEW.salary <> OLD.salary THEN
        INSERT INTO salary_audit (employee_id, old_salary, new_salary, changed_on)
        VALUES (OLD.id, OLD.salary, NEW.salary, now());
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### **Creating a Trigger**
```sql
CREATE TRIGGER salary_update
BEFORE UPDATE ON employees
FOR EACH ROW
EXECUTE FUNCTION audit_salary_changes();
```

### **Trigger Types Quick Reference**
```sql
CREATE TRIGGER IF NOT EXISTS trigger_name
{ BEFORE | AFTER } { INSERT | UPDATE | DELETE | TRUNCATE }
ON table_name
FOR EACH { ROW | STATEMENT }
EXECUTE FUNCTION trigger_function();
```

---

## 5. Cursors
Used to iterate over result sets row by row.

### **Using Cursors with OPEN-FETCH-CLOSE and FOR Loop**

#### **Using OPEN-FETCH-CLOSE**
```sql
DO $$
DECLARE
    emp_cursor CURSOR FOR SELECT id, name FROM employees;
    emp RECORD;
BEGIN
    OPEN emp_cursor;
    LOOP
        FETCH emp_cursor INTO emp;
        EXIT WHEN NOT FOUND;
        RAISE NOTICE 'Employee: %', emp.name;
    END LOOP;
    CLOSE emp_cursor;
END $$ LANGUAGE plpgsql;
```

#### **Using FOR Loop (Simplified Cursor Handling)**
```sql
DO $$
BEGIN
    FOR emp IN (SELECT id, name FROM employees) LOOP
        RAISE NOTICE 'Employee: %', emp.name;
    END LOOP;
END $$ LANGUAGE plpgsql;
```

---

## 6. Functions & Stored Procedures

### **Function Example (Returns a Value)**
```sql
CREATE FUNCTION get_employee_name(emp_id INT) RETURNS TEXT AS $$
DECLARE emp_name TEXT;
BEGIN
    SELECT name INTO emp_name FROM employees WHERE id = emp_id;
    RETURN emp_name;
EXCEPTION
    WHEN no_data_found THEN
        RETURN 'No employee found!';
END;
$$ LANGUAGE plpgsql;
```

### **Procedure Example (No Return, Just Executes)**
```sql
CREATE PROCEDURE update_salary(emp_id INT, new_salary NUMERIC) AS $$
BEGIN
    UPDATE employees SET salary = new_salary WHERE id = emp_id;
    COMMIT;
END;
$$ LANGUAGE plpgsql;
```

### **Calling a Procedure**
```sql
CALL update_salary(5, 50000);
```

---

## 7. Updating & Deleting with Cursors

### **Using a Cursor to Update Data**
```sql
DO $$
DECLARE
    emp_cursor CURSOR FOR SELECT id FROM employees WHERE department = 'HR';
BEGIN
    FOR emp IN emp_cursor LOOP
        UPDATE employees SET salary = salary * 1.10 WHERE CURRENT OF emp_cursor;
    END LOOP;
END $$ LANGUAGE plpgsql;
```

### **Using `FOR UPDATE` in Cursors**

`FOR UPDATE` locks the selected rows to prevent other transactions from modifying them until the cursor is closed or the transaction is completed. This is useful when ensuring that data remains consistent while processing multiple rows.

**Difference Between `FOR UPDATE` and a Normal Cursor**:

A normal cursor, like in the function below, simply retrieves data but does not lock rows:
```sql
CREATE FUNCTION get_employee_name(emp_id INT) RETURNS TEXT AS $$
DECLARE emp_name TEXT;
BEGIN
    SELECT name INTO emp_name FROM employees WHERE id = emp_id;
    RETURN emp_name;
EXCEPTION
    WHEN no_data_found THEN
        RETURN 'No employee found!';
END;
$$ LANGUAGE plpgsql;
```

However, if we modify it to use `FOR UPDATE`, it will lock the row to prevent changes from other transactions:
```sql
CREATE FUNCTION get_employee_name_locked(emp_id INT) RETURNS TEXT AS $$
DECLARE emp_name TEXT;
BEGIN
    SELECT name INTO emp_name FROM employees WHERE id = emp_id FOR UPDATE;
    RETURN emp_name;
EXCEPTION
    WHEN no_data_found THEN
        RETURN 'No employee found!';
END;
$$ LANGUAGE plpgsql;
```
With `FOR UPDATE`, other transactions will not be able to modify or delete the retrieved row until the function completes.

`FOR UPDATE` locks the selected rows to prevent other transactions from modifying them until the cursor is closed or the transaction is completed. This is useful when ensuring that data remains consistent while processing multiple rows.
```sql
DECLARE emp_cursor CURSOR FOR
SELECT id FROM employees WHERE department = 'IT' FOR UPDATE;
```

---

## 8. Renaming & Removing Objects

### **Renaming a Trigger**
```sql
ALTER TRIGGER old_trigger_name ON employees RENAME TO new_trigger_name;
```

### **Dropping Objects**
```sql
DROP FUNCTION get_employee_name;
DROP PROCEDURE update_salary;
DROP TRIGGER salary_update ON employees;
```

---

## 9. Debugging Tips

### **Printing Debug Messages**
```sql
RAISE NOTICE 'Debug: value = %', var_name;
```

### **Checking for NULL Values**
```sql
IF var_name IS NULL THEN
    RAISE NOTICE 'Value is NULL!';
END IF;
```

