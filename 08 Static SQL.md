# Leveraging Static SQL in Oracle PL/SQL 

The tight integration between PL/SQL and Oracle SQL permits us to use SQL in two ways: Static and Dynamic SQL. This guide will primarily focus on Static SQL, diving into SELECT statements, DML statements, and transaction control statements in a PL/SQL environment.

---

## Table of Contents
- [Running SELECT Statements](#running-select-statements)
- [Executing DML Statements](#executing-dml-statements)
- [Handling Transactions](#handling-transactions)
- [Using Built-In Variables](#using-built-in-variables)
- [Working with Sequences](#working-with-sequences)

---

<a id="running-select-statements"></a>
## Running SELECT Statements
In PL/SQL, SELECT statements work exactly like in a standalone SQL environment, with one significant difference: PL/SQL requires the output of a SELECT statement to be stored somewhere, typically a variable. The INTO clause is used to facilitate this. For example:

```plsql
DECLARE 
  emp_count NUMBER(5);
BEGIN
  SELECT COUNT(*) INTO emp_count 
  FROM employees;
  
  DBMS_OUTPUT.PUT_LINE('Employee count: ' || emp_count);
END;
```

In this example, the `emp_count` variable is declared, and when the SELECT statement runs, the `INTO` clause directs the row count to `emp_count`.

---

<a id="executing-dml-statements"></a>
## Executing DML Statements
PL/SQL accommodates all the typical DML (Data Manipulation Language) statements you would use in SQL - `INSERT`, `UPDATE`, `DELETE`, and `MERGE`. They run in PL/SQL as they would in a standalone SQL environment.

Here's an example of an `INSERT` statement:

```plsql
BEGIN
  INSERT INTO departments_archive 
  SELECT * FROM departments;
  
  DBMS_OUTPUT.PUT_LINE('Departments archived.');
EXCEPTION 
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('An error occurred: ' || SQLERRM);
END;
```

In the event of an error with the DML statement, you can use an EXCEPTION block to handle the error and potentially execute alternative code.

---

<a id="handling-transactions"></a>
## Handling Transactions
Transactions in PL/SQL work exactly the same as in a standalone SQL environment. You can run a `COMMIT` to save changes from outstanding DML statements, or a `ROLLBACK` under specific conditions. Here is an example:

```plsql
BEGIN
  INSERT INTO departments_archive 
  SELECT * FROM departments;
  
  COMMIT;  -- Make changes permanent
EXCEPTION 
  WHEN OTHERS THEN
    ROLLBACK;  -- Rollback if error occurs
    DBMS_OUTPUT.PUT_LINE('An error occurred: ' || SQLERRM);
END;
```

Once the `COMMIT` is run in the PL/SQL block, the changes to the table are permanent and visible to other sessions.

---

<a id="using-built-in-variables"></a>
## Using Built-In Variables
There are built-in PL/SQL variables that you can reference after executing DML statements. `SQL%ROWCOUNT` holds the count of the number of rows affected by the last DML statement:

```plsql
DECLARE 
  rows_updated NUMBER(5);
BEGIN
  UPDATE employees SET salary = salary * 1.1 
  WHERE department_id = 30;
  
  rows_updated := SQL%ROWCOUNT;
  
  DBMS_OUTPUT.PUT_LINE(rows_updated || ' rows updated.');
END;
```

Other built-in variables include `SQL%FOUND` (returns `TRUE` if the last SQL operation affected one or more rows), `SQL%NOTFOUND` (returns `TRUE` if the last SQL operation did not affect any rows), and `SQL%ISOPEN` (used with explicit cursors and returns `TRUE` if the cursor is open).

---

<a id="working-with-sequences"></a>
## Working with Sequences
Sequences in PL/SQL operate similarly as in standalone SQL. The pseudo-variables `CURRVAL` and `NEXTVAL` can be assigned to variables even in the absence of a DML operation:

```plsql
DECLARE 
  curr_seq_val NUMBER(10);
  next_seq_val NUMBER(10);
BEGIN
  SELECT my_sequence.CURRVAL, my_sequence.NEXTVAL 
  INTO curr_seq_val, next_seq_val FROM dual;
  
  DBMS_OUTPUT.PUT_LINE('Current value: ' || curr_seq_val);
  DBMS_OUTPUT.PUT_LINE('Next value: ' || next_seq_val);
END;
```

The pseudo-variables `CURRVAL` and `NEXTVAL` offer capabilities in PL/SQL that they don't provide in standalone SQL, including storing their values without invoking them in a DML operation.

---

Remember, PL/SQL's close integration with SQL provides great flexibility and control, allowing you to leverage SQL within your PL/SQL programs effortlessly. As shown, the majority of SQL operations work seamlessly within PL/SQL, with the added benefit of using PL/SQL variables and control structures.
