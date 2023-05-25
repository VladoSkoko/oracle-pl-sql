# Utilizing SELECT INTO and %ROWCOUNT Constructs in Oracle PL/SQL

The power of PL/SQL lies in its ability to execute any SELECT query or DML statement written in Oracle 19c SQL in multiple ways. This lesson introduces you to the usage of column data retrieved with SELECT INTO and DML statements with built-in variables such as SQL%ROWCOUNT and SQL%FOUND.

## Table of Contents
- [SELECT INTO Construct](#select-into)
- [DML Statements and Status Variables](#dml-statements)
- [Retrieving Multiple Rows with Bulk Collect](#bulk-collect)
- [Integration of PL/SQL with Oracle SQL for DML Statements](#plsql-oracle-integration)

<a name="select-into"></a>
## SELECT INTO Construct

PL/SQL's integration of Oracle SQL and various programming constructs such as cursors, for loops, and conditional statements makes it possible to use SELECT statements and familiar DML statements (INSERT, DELETE, UPDATE, MERGE, etc.). However, SELECT statements require special consideration because they fetch data that should be stored or utilized in some way. 

Consider the following anonymous procedure:

```plsql
DECLARE
  v_emp_id employees.employee_id%TYPE;
  v_last_name employees.last_name%TYPE;
BEGIN
  SELECT employee_id, last_name 
  INTO v_emp_id, v_last_name 
  FROM employees 
  WHERE first_name = 'Dan';
  
  dbms_output.put_line('Employee ID: ' || v_emp_id || ', Last Name: ' || v_last_name);
END;
```

This procedure uses a SELECT INTO statement to retrieve the employee ID and last name for an employee with the first name 'Dan'. It then stores this data in two local variables `v_emp_id` and `v_last_name`.

The SELECT INTO statement can only return one row. If the query matches multiple rows, it will throw an error. If there are multiple matches expected, you should use a cursor or the BULK COLLECT feature instead.

<a name="dml-statements"></a>
## DML Statements and Status Variables

When executing DML statements, PL/SQL provides built-in status variables that help to understand the effect of the last executed DML statement. These variables include:

- `SQL%ROWCOUNT`: Returns the number of rows affected by the DML statement.
- `SQL%FOUND`: Returns TRUE if the DML statement affected at least one row, otherwise FALSE.
- `SQL%NOTFOUND`: Returns TRUE if no rows were affected by the DML statement, otherwise FALSE.

These status variables can be used to save the effect of DML statements as shown in the example below:

```plsql
DECLARE
  row_count NUMBER;
BEGIN
  INSERT INTO departments_archive
  SELECT * FROM departments;
  
  row_count := SQL%ROWCOUNT;

  dbms_output.put_line('Number of rows inserted: ' || row_count);
END;
```

In this code snippet, the number of rows affected by the INSERT statement is stored in the `row_count` variable using the SQL%ROWCOUNT status variable.

<a name="bulk-collect"></a>
## Retrieving Multiple Rows with Bulk Collect

In scenarios where a SELECT query is expected to return multiple rows, the BULK COLLECT clause is used to store the result set in a PL/SQL collection. Here is how to use BULK COLLECT:

```plsql
DECLARE
  TYPE t_emp_table IS TABLE OF employees%ROWTYPE;
  emp_table t_emp_table;
BEGIN
  SELECT * 
  BULK COLLECT INTO emp_table 
  FROM employees;
  
  dbms_output.put_line('Number of employees: ' || emp_table.COUNT);
END;
```

In this example, the BULK COLLECT clause is used to fetch all rows from the employees table and store them in the `emp_table` PL/SQL collection.

<a name="plsql-oracle-integration"></a>
## Integration of PL/SQL with Oracle SQL for DML Statements

Any SELECT query or DML statement you write in Oracle 19c SQL can be executed in PL/SQL. While SELECT statements require SELECT INTO or BULK COLLECT for handling the returned rows, DML statements can run unaltered in PL/SQL, which is a significant advantage for translating ad hoc scripts into stored procedures and functions.

```plsql
BEGIN
  INSERT INTO departments_archive
  SELECT * FROM departments;

  dbms_output.put_line('Number of rows inserted: ' || SQL%ROWCOUNT);
  
  DELETE FROM departments_archive
  WHERE department_id = 10;
  
  dbms_output.put_line('Number of rows deleted: ' || SQL%ROWCOUNT);
END;
```

In this example, we first execute an INSERT statement and use SQL%ROWCOUNT to show the number of rows inserted into `departments_archive`. Then we run a DELETE statement, and again, use SQL%ROWCOUNT to display the number of rows deleted.
