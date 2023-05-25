# Leveraging Dynamic SQL in Oracle PL/SQL

In Oracle PL/SQL, while static SQL is an effective way to execute a large subset of SQL statements, there are scenarios where dynamic SQL becomes necessary. For instance, when you need to run a SQL statement that's not natively supported by PL/SQL, or when the SQL statement's structure is unknown until runtime. 

## Table of Contents
- [Understanding Static and Dynamic SQL](#understanding-static-and-dynamic-sql)
- [Advantages of Static SQL](#advantages-of-static-sql)
- [Syntax for Dynamic SQL](#syntax-for-dynamic-sql)
- [Using Execute Immediate Statement](#using-execute-immediate-statement)
- [Using the 'USING' Clause](#using-the-using-clause)
- [Advanced Examples of Dynamic SQL](#advanced-examples-of-dynamic-sql)
- [When to Use Dynamic SQL](#when-to-use-dynamic-sql)

## Understanding Static and Dynamic SQL
<a id='understanding-static-and-dynamic-sql'></a>

Static SQL allows for running a majority of SQL statements natively in PL/SQL, such as `INSERT`, `UPDATE`, `DELETE`, and `MERGE`. However, there are SQL statements that can't be run natively inside of PL/SQL. These include primarily DDL statements (`CREATE`, `DROP`, `GRANT`, `REVOKE`, `TRUNCATE`, etc.), and session control statements (`ALTER SESSION`, `ALTER SYSTEM`, `SET ROLE`, etc.).

Dynamic SQL, in contrast, provides flexibility and allows the construction of SQL statements dynamically at runtime, which is particularly useful when the structure of the SQL statement is unknown at compile time. For example, if you don't know what columns you want to query at compile time, or what table name you're querying, or how you're going to filter rows, these queries can be dynamically constructed at runtime using the `EXECUTE IMMEDIATE` statement.

```sql
DECLARE
  sql_stmt    VARCHAR2(200);
  my_empid    NUMBER;
BEGIN
  sql_stmt := 'SELECT empid FROM employees WHERE lastname = :1';
  EXECUTE IMMEDIATE sql_stmt INTO my_empid USING 'Smith';
  DBMS_OUTPUT.PUT_LINE('Employee ID: ' || my_empid);
END;
```

## Advantages of Static SQL
<a id='advantages-of-static-sql'></a>

Although dynamic SQL provides flexibility, there are reasons to prefer static SQL:

1. **Error Detection at Compile Time**: With static SQL, more errors can be detected at compile time since the PL/SQL processor knows the exact text of the statement.
2. **Lower Overhead**: The context switching between SQL and PL/SQL has lower overhead.
3. **Security**: Using dynamic SQL might open doors for security breaches if some or all of the string provided in `EXECUTE IMMEDIATE` can't be controlled, such as by a user input that ends up in a `WHERE` clause.

## Syntax for Dynamic SQL
<a id='syntax-for-dynamic-sql'></a>

The syntax for dynamic SQL, specifically with `EXECUTE IMMEDIATE`, is slightly more complex than that for native SQL. The clause for a dynamic SQL statement can be a string constant or a string variable declared in the procedure. It's typically a `VARCHAR2` variable but can also be `CHAR` or even a `CLOB`. 

For returning a single row, the `INTO` clause is used. If you expect to return a row set consisting of multiple rows, the `BULK COLLECT INTO` clause is used. The `USING` clause lets you specify substitution variables outside of the SQL statement's text.

## Using the EXECUTE IMMEDIATE Statement
<a id='using-execute-immediate-statement'></a>

The `EXECUTE IMMEDIATE` statement is used to execute dynamic SQL and PL/SQL statements which are stored in a string. 

```sql
DECLARE
  dynamic_string VARCHAR2(200);
BEGIN
  dynamic_string := 'CREATE TABLE temp_table (id NUMBER)';
  EXECUTE IMMEDIATE dynamic_string;
END;
```

In the above code snippet, `EXECUTE IMMEDIATE` is used to create a table named `temp_table`. The SQL statement to create the table is stored in the `dynamic_string` variable, which is passed as a parameter to `EXECUTE IMMEDIATE`.

## Using the USING Clause
<a id='using-the-using-clause'></a>

The `USING` clause in `EXECUTE IMMEDIATE` allows you to pass variables to the SQL statement being executed. 

```sql
DECLARE
  dynamic_string VARCHAR2(200);
  my_var NUMBER := 100;
BEGIN
  dynamic_string := 'UPDATE my_table SET column1 = :1 WHERE column2 = 10';
  EXECUTE IMMEDIATE dynamic_string USING my_var;
END;
```

In the above example, `my_var` is passed to the SQL statement, replacing the `:1` bind variable.

## Advanced Examples of Dynamic SQL
<a id='advanced-examples-of-dynamic-sql'></a>

Consider a scenario where we dynamically construct a `SELECT` statement using variables.

```sql
DECLARE
  column_name VARCHAR2(30) := 'lastname';
  column_value VARCHAR2(30) := 'Smith';
  sql_stmt VARCHAR2(200);
  emp_id NUMBER;
BEGIN
  sql_stmt := 'SELECT empid FROM employees WHERE ' || column_name || ' = :1';
  EXECUTE IMMEDIATE sql_stmt INTO emp_id USING column_value;
  DBMS_OUTPUT.PUT_LINE('Employee ID: ' || emp_id);
END;
```

In the example above, the `column_name` and `column_value` variables are used to dynamically construct the `SELECT` statement.

## When to Use Dynamic SQL
<a id='when-to-use-dynamic-sql'></a>

Dynamic SQL should only be used when static SQL is insufficient. This typically includes DDL statements (`CREATE`, `DROP`, etc.), and permissions management (`GRANT`, `REVOKE`, etc.). 

Remember, if you want to return a value or values from `EXECUTE IMMEDIATE`, add an `INTO` clause. If you want to pass variables to `EXECUTE IMMEDIATE`, use bind variables in the statement clause and enumerate them with the `USING` clause.

Refer to the [Oracle PL/SQL Language Reference](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/EXECUTE-IMMEDIATE-statement.html) for more in-depth coverage of these topics.
