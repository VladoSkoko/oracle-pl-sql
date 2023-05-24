# PL/SQL Declarations: Understanding Variables and Cursors in Oracle 

In PL/SQL, the declaration section allows for declaring variables and cursors, playing a crucial role in creating efficient functions and procedures. The tutorial delves into the significance and usage of these declarations, showing how to implement them in your PL/SQL code, with specific attention to Oracle 19c SQL.

## Table of Contents

- [Variables Declaration in PL/SQL](#Variables-Declaration-in-PLSQL)
- [Cursors Declaration in PL/SQL](#Cursors-Declaration-in-PLSQL)

## Variables Declaration in PL/SQL <a name="Variables-Declaration-in-PLSQL"></a>

When constructing either a function or a procedure in PL/SQL, the declaration syntax stays constant. The syntax's four main areas are the procedure name, the declare section, the executable section, and an optional exception section. 

A common misconception is that a PL/SQL block starts with the `DECLARE` keyword. In reality, `DECLARE` is only used in anonymous blocks — procedures that are not stored and only run in SQL Plus, SQLcl, or SQL Developer. Otherwise, the declaration section begins with `IS` or `AS`.

Here's an overview of a PL/SQL procedure:

```plsql
CREATE PROCEDURE procedure_name AS
DECLARE
  -- declarations
BEGIN
  -- PL/SQL statements
EXCEPTION
  -- exception handling
END;
```

In the declaration section, you can declare variables of any data type available in a table definition, demonstrating the tight integration between Oracle 19c SQL and PL/SQL. Furthermore, this section can also include assignments of default values to variables, helping enhance the code's efficiency.

Here's an example of variable declaration and assignment in the declare section:

```plsql
DECLARE 
  n10 NUMBER := 152;
BEGIN
  DBMS_OUTPUT.PUT_LINE(n10);
END;
```

Moreover, if you wish to use a variable throughout the procedure without changing it, you can define it as a `CONSTANT`. Although it may seem paradoxical, the `CONSTANT` keyword helps maintain the procedure's integrity by preserving particular variable values.

Here's how to declare a constant in PL/SQL:

```plsql
DECLARE
  pi CONSTANT NUMBER := 3.14;
  circumference NUMBER;
BEGIN
  circumference := 2 * pi * radius;
  -- The following statement would raise an error
  -- pi := 3.14159;
END;
```

Using the `%TYPE` and `%ROWTYPE` attributes can help your code adapt to changes in your table or column attributes. The `%TYPE` attribute allows a variable to inherit the data type of a specific column, while the `%ROWTYPE` attribute defines a record that can reference all the columns of a specific table.

Here's how you can use `%TYPE` and `%ROWTYPE`:

```plsql
DECLARE
  nameval employees.last_name%TYPE;
  emperec employees%ROWTYPE;
BEGIN
  emperec.employee_id := 100;
  emperec.last_name := 'King';
  DBMS_OUTPUT.PUT_LINE('Employee: ' || emperec.employee_id || ', ' || emperec.last_name);
END;
```

## Cursors Declaration in PL/SQL <a name="Cursors-Declaration-in-PLSQL"></a>

Cursors, akin to pointers, are another element that you might declare in your PL/SQL block. They can be linked to either a `SELECT` statement or any DML statement and can be declared in the declaration section or between the `BEGIN` and `END` blocks. 

An explicit cursor requires a cursor declaration, allowing you to step through the rows of a cursor with various types of commands. An implicit cursor does not require a declaration, and you can still retrieve information from the cursor.

Here's an example of an explicit cursor:

```plsql
DECLARE
  CURSOR empcur IS SELECT * FROM employees;
  nameval employees.last_name%TYPE;
BEGIN
  FOR rec IN empcur LOOP
    nameval := rec.last_name;
    DBMS_OUTPUT.PUT_LINE(nameval);
  END LOOP;
END;
```

An implicit cursor does not need to be declared in the declare section. Instead, it is used just once in the `FOR` loop with a `SELECT` statement, and it produces the same results as an explicit cursor.

Here's an example of an implicit cursor:

```plsql
BEGIN
  FOR rec IN (SELECT * FROM employees) LOOP
    DBMS_OUTPUT.PUT_LINE(rec.last_name);
  END LOOP;
END;
```

When using an implicit cursor, the `%ROWCOUNT` variable lets you know how many rows were affected by the statement, while `%FOUND` checks if any rows were returned from a statement. For example:

```plsql
DECLARE
  rows_updated NUMBER;
BEGIN
  UPDATE employees SET salary = salary * 1.1 WHERE department_id = 10;
  rows_updated := SQL%ROWCOUNT;
  DBMS_OUTPUT.PUT_LINE('Rows updated: ' || rows_updated);
END;
```

In summary, whether you declare cursors explicitly in the declare section or implicitly in the `BEGIN` and `END` section, each approach has its benefits and use-cases. Understanding how and when to use each one can optimize your PL/SQL procedures and functions. 
