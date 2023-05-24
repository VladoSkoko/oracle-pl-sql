# Overview of PL/SQL in Oracle 19c

PL/SQL is a programming language built into Oracle 19c, expanding your capabilities to control the Oracle database more effectively. It is a powerful third-generation programming language, supporting advanced programming features such as variable declarations, code scope limitation, iteration within row sets or result sets using for loops, and conditional statements. With a tight integration between SQL and PL/SQL, Oracle 19c enables us to leverage data types common to both, and to write procedures that can dynamically handle unknown table structures. 

## Table of Contents
- [Distinguishing SQL and PL/SQL](#distinguishing-sql-and-plsql)
- [Exploring PL/SQL Language Elements](#exploring-plsql-language-elements)
- [Handling Exceptions in PL/SQL](#handling-exceptions-in-plsql)
- [Inline PL/SQL Function in a SELECT Statement](#inline-plsql-function-in-a-select-statement)
- [Final Thoughts](#final-thoughts)

<a name="distinguishing-sql-and-plsql"></a>
## Distinguishing SQL and PL/SQL

Understanding the distinction between SQL and PL/SQL is crucial. SQL is a declarative language, meaning that you specify what you want done, and the SQL processor determines how to execute it most efficiently. For example, consider the following SQL query:

```sql
SELECT employee_id 
FROM employees 
UNION ALL 
SELECT employee_id 
FROM emp_history;
```

In this case, you want to return all the `employee_id` values from the `employees` and `emp_history` tables. The SQL processor then determines the best way to achieve this.

PL/SQL, on the other hand, is a procedural language. It lets you define a sequence of operations that the database should follow, providing explicit instructions to fetch the data. For instance:

```plsql
BEGIN
  FOR emp IN (SELECT employee_id FROM employees) LOOP
    DBMS_OUTPUT.PUT_LINE(emp.employee_id);
  END LOOP;

  FOR emp_hist IN (SELECT employee_id FROM emp_history) LOOP
    DBMS_OUTPUT.PUT_LINE(emp_hist.employee_id);
  END LOOP;
END;
```

<a name="exploring-plsql-language-elements"></a>
## Exploring PL/SQL Language Elements

There are several key elements in the PL/SQL language:

1. **Procedure:** A procedure is a stored block of PL/SQL code that can be called with zero or more variables.

```plsql
PROCEDURE print_employee_names AS 
BEGIN 
  FOR emp IN (SELECT employee_name FROM employees) LOOP 
    DBMS_OUTPUT.PUT_LINE(emp.employee_name); 
  END LOOP; 
END print_employee_names;
```

2. **Function:** A function explicitly declares a return value. You can call functions from SQL, such as `sqrt()` or `sum()`.

```plsql
FUNCTION square_root (n NUMBER) RETURN NUMBER AS 
BEGIN 
  RETURN SQRT(n); 
END square_root;
```

3. **Loops:** `FOR` and `WHILE` loops let you iterate over a result set, defined with a cursor and a SELECT statement.

```plsql
BEGIN
  FOR i IN 1..10 LOOP
    DBMS_OUTPUT.PUT_LINE(i);
  END LOOP;
END;
```

4. **Conditional Statements:** `IF-THEN-ELSE` statements can be used to check and compare values in any column of a retrieved row.

```plsql
BEGIN
  FOR emp IN (SELECT salary FROM employees WHERE employee_id = 123) LOOP
    IF emp.salary > 50000 THEN
      DBMS_OUTPUT.PUT_LINE('High salary');
    ELSE
      DBMS_OUTPUT.PUT_LINE('Low salary

');
    END IF;
  END LOOP;
END;
```

<a name="handling-exceptions-in-plsql"></a>
## Handling Exceptions in PL/SQL

In the real world, things can go wrong. PL/SQL provides robust exception handling to deal with unexpected conditions or errors in the data. For instance:

```plsql
BEGIN
  -- Some PL/SQL code here
EXCEPTION
  WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('No data found!');
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('An unknown error occurred!');
END;
```

<a name="inline-plsql-function-in-a-select-statement"></a>
## Inline PL/SQL Function in a SELECT Statement

Sometimes you need to use PL/SQL functions directly in your SQL code. You can do this in the `WITH` clause. This is especially useful for portability or when you can't store your own functions. Additionally, it reduces the number of context switches from SQL to PL/SQL, which can benefit performance.

Here's an example of an inline PL/SQL function replacing embedded spaces in a last name with a period:

```sql
WITH
  FUNCTION format_name(last_name VARCHAR2) RETURN VARCHAR2 IS
  BEGIN
    RETURN REPLACE(last_name, ' ', '.');
  END;
SELECT format_name(last_name) AS formatted_name
FROM employees;
```

<a name="final-thoughts"></a>
## Final Thoughts

PL/SQL in Oracle 19c provides a robust toolset for complex data manipulation that goes beyond what's possible in SQL alone. It includes all features of modern programming languages like variable declaration, loops, and conditional branching. All Oracle SQL statements (SELECT, DELETE, INSERT, UPDATE) can be seamlessly integrated into PL/SQL, offering you more control over their execution. Moreover, PL/SQL's ability to construct SQL statements dynamically at runtime significantly expands the flexibility of your data manipulation procedures.
