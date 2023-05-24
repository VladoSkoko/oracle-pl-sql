# PL/SQL Security Model and Code Scope in Oracle

This guide provides a look into the security model of PL/SQL in Oracle, focusing on definer rights vs invoker rights, and explaining how blocks define code scope. It also showcases a number of practical examples that illustrate these concepts.

## Table of Contents
- [Definer Rights vs Invoker Rights](#definer-rights-vs-invoker-rights)
- [The Role of Database Roles](#the-role-of-database-roles)
- [How Blocks Define Scope](#how-blocks-define-scope)

## Definer Rights vs Invoker Rights <a name="definer-rights-vs-invoker-rights"></a>

When creating procedures and functions in PL/SQL, an important decision is choosing between definer or invoker rights for the procedure itself. In a nutshell, **definer rights** means that the procedure executes with the privileges of whoever created it, whereas **invoker rights** means the procedure runs with the privileges of the user who is invoking it. By default, procedures run with definer rights.

This distinction comes into play in the `CREATE PROCEDURE` command with the `AUTHID` clause. Let's take a look at a brief example:

```sql
-- A procedure with definer rights
CREATE OR REPLACE PROCEDURE count_EMP AUTHID DEFINER AS
BEGIN
  -- Count rows in the employees table
END;

-- A procedure with invoker rights
CREATE OR REPLACE PROCEDURE count_EMP AUTHID CURRENT_USER AS
BEGIN
  -- Count rows in the employees table
END;
```
The above `count_EMP` procedure counts how many rows are in the employees table. When the procedure is created with definer rights, it will run with the privileges of the user who created it. However, if it's created with invoker rights (specified by `AUTHID CURRENT_USER`), it will run with the privileges of the user who invokes it.

Let's assume we have two users, `HR` and `OE`, both of which have a copy of the employees table. `HR`'s table has 107 rows, while `OE`'s table is empty. Even if the procedure is owned by `HR`, if `OE` invokes a procedure created with invoker rights, it will execute under `OE`'s privileges and thus count zero rows. But if `OE` invokes a procedure with definer rights, it will count 107 rows because the procedure will run with the privileges of `HR` (who is the definer).

To ensure that the procedure always reads from the `HR` schema, we can qualify the table with the schema name. 

```sql
CREATE OR REPLACE PROCEDURE count_EMP AUTHID DEFINER AS
BEGIN
  -- Always count rows from the HR's employees table
END;
```
With this adjustment, `OE` can still execute the procedure and it will return 107 rows, even though `OE` doesn't have object privileges on `HR`'s employees table. 

## The Role of Database Roles <a name="the-role-of-database-roles"></a>

Database roles in PL/SQL also play a crucial part in privileges. Roles are essentially a grouping of privileges that can be granted as a unit to a user or another role. However, there's a subtle caveat with roles when dealing with procedures: if a procedure is created with definer rights, all roles for the definer are disabled.

```sql
CREATE OR REPLACE PROCEDURE update_jobs AUTHID DEFINER AS
BEGIN
  -- Update operations
EXCEPTION
  WHEN OTHERS THEN
    RAISE;
END;
```
If you try to run SQL commands inside a procedure with definer rights, you might discover that the commands don't have the expected privileges because the roles have been disabled. However, if the block is an anonymous block, or a named block with invoker rights, all default roles are enabled as expected.

To get around this, you'd have to grant privileges on database objects directly to the user who needs them, such as the Select Object privilege on `hr.jobs`.

## How Blocks Define Scope <a name="how-blocks-define-scope"></a>

PL/SQL blocks are an effective programming technique for isolating certain sections of code and enhancing code transportability and robustness. A PL/SQL block, in its simplest form, is a set of code encapsulated between the `BEGIN` and `END` keywords. Labels can be used for easy identification of the blocks, especially when working with nested blocks.

```sql
<<outer_block>>
DECLARE
  samename NUMBER;
BEGIN
  samename := 10;

  <<inner_block>>
  DECLARE
    samename NUMBER;
  BEGIN
    samename := 20;
    DBMS_OUTPUT.PUT_LINE('Inner block samename: ' || inner_block.samename);
    DBMS_OUTPUT.PUT_LINE('Outer block samename: ' || outer_block.samename);
  END inner_block;
END outer_block;
```
In this example, the outer block and the inner block both have a variable named `samename`. By default, a variable reference is to the innermost block where it's declared, but in this case, variables are qualified with their respective block names. This practice helps avoid ambiguity and enhances code readability, especially in scenarios where nested blocks are involved.
