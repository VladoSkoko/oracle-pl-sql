# Mastering PL/SQL in Oracle Database 19c 

Welcome to this guide on PL/SQL within the Oracle Database 19c. In this course, we'll be covering everything from the basic constructs of PL/SQL to advanced programming concepts. As you learn, you'll become more adept at writing procedures and functions, managing control structures such as loops and conditional statements, handling exceptions, and effectively performing Data Manipulation Language (DML) operations. 

PL/SQL, a programming language embedded in the Oracle Database 19c, is indispensable for using key database features. By grasping the principles of this language, you'll find it simpler to enforce business rules, apply procedural constructs, and get the most out of your database. 

## Table of Contents
- [Prerequisites](#prerequisites)
- [Environment Setup](#environment-setup)
- [Course Overview](#course-overview)

<a name="prerequisites"></a>
## Prerequisites

Before diving into this course, it's beneficial to have some foundational knowledge:

1. **Relational Database Concepts:** You should understand the basic principles of relational databases. We will discuss some of these concepts, but prior knowledge will enhance your learning experience.

```sql
SELECT * FROM employees;
```

2. **Oracle SQL:** It's necessary to know the basics of Oracle SQL. You can achieve this by taking Oracle Database 19c Basic and Advanced SQL courses.

```sql
UPDATE employees
SET salary = salary + 500
WHERE department_id = 100;
```

3. **Transactions:** You should grasp the concept of a database transaction, including when changes to the database need to be rolled back.

```sql
BEGIN
  UPDATE accounts
  SET balance = balance - 100
  WHERE account_id = 123;
  
  SAVEPOINT my_savepoint;
  
  UPDATE accounts
  SET balance = balance + 100
  WHERE account_id = 456;
  
  -- Error occurs, rollback to savepoint
  ROLLBACK TO my_savepoint;
  
  COMMIT;
END;
```

4. **Programming:** Experience with a modern procedural language such as C++, Java, or any similar language is extremely beneficial.

5. **Client-Server Environments:** Understand how client-server environments operate and how cloud architecture extends that environment transparently.

6. **OS Command Prompt Familiarity:** Be comfortable using the command prompt for Linux or Windows to run SQLplus and SQLcl command-line examples.

<a name="environment-setup"></a>
## Environment Setup

To get the most out of this course, you'll need to set up your learning environment accordingly:

1. **Oracle Database 19c:** You need access to an Enterprise Edition of Oracle Database 19c. This can be installed on a Linux Virtual Machine (VM), or your local DBA could create one for you.

2. **Sample Schemas:** Install the sample schemas, with the HR schema being the minimum requirement.

```sql
CONN / AS SYSDBA
@$ORACLE_HOME/demo/schema/human_resources/hr_main.sql
```

3. **GUI Environment:** You'll need a GUI environment to run SQL Developer. For Windows users, this will be automatically available. If your Oracle Database runs on Linux, your PC or workstation will need an X Windows server installed.

4. **SQL Developer:** SQL Developer is an essential tool for this course. It provides a rich development and debugging environment for PL/SQL code.

<a name="course-overview"></a>
## Course Overview

In the upcoming sections, we will explore the depths of Oracle PL/SQL, focusing on key aspects such as:

1. **Functions and Procedures:** Understand how to create and call PL/SQL functions and procedures.

```

plsql
CREATE OR REPLACE FUNCTION total_sales (p_deptno NUMBER)
RETURN NUMBER
IS
  v_total_sales NUMBER(9, 2);
BEGIN
  SELECT SUM(amount_sold)
  INTO v_total_sales
  FROM sales s JOIN products p ON s.prod_id = p.prod_id
  WHERE p.prod_dept = p_deptno;
  
  RETURN v_total_sales;
END;
```

2. **Control Structures:** Learn how to implement control structures such as loops and conditional statements.

```plsql
BEGIN
  FOR i IN 1..10 LOOP
    IF i MOD 2 = 0 THEN
      DBMS_OUTPUT.PUT_LINE(i || ' is even');
    ELSE
      DBMS_OUTPUT.PUT_LINE(i || ' is odd');
    END IF;
  END LOOP;
END;
```

3. **Exception Handling:** Learn to handle exceptions effectively in PL/SQL.

```plsql
BEGIN
  -- some PL/SQL code here
EXCEPTION
  WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('No data found in the table');
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('An unexpected error occurred');
END;
```

4. **Data Manipulation Language (DML):** Learn to effectively use DML statements to manipulate data in Oracle Database.

```plsql
BEGIN
  INSERT INTO employees (employee_id, first_name, last_name)
  VALUES (123, 'John', 'Doe');
  
  UPDATE employees
  SET salary = salary + 500
  WHERE employee_id = 123;
  
  DELETE FROM employees
  WHERE employee_id = 123;
  
  COMMIT;
END;
```

By the end of this course, you'll be able to harness the power of PL/SQL in Oracle Database 19c, enabling you to work more efficiently with your database, spend less time debugging issues, and focus more on the design and implementation of your database structure. Happy learning!
