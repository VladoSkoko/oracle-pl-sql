# Understanding PL/SQL Data Types

This guide will introduce you to various data types, including Blobs, Clobs, BOOLEAN, and PLS_INTEGER, focusing on their declaration, usage, and compatibility with native SQL.

## Table of Contents
- [Understanding Basic Data Types](#understanding-basic-data-types)
- [Working with Large Objects: BLOBs and CLOBs](#working-with-large-objects-blobs-and-clobs)
- [Using PL/SQL Exclusive Data Types](#using-plsql-exclusive-data-types)
  - [The BOOLEAN Data Type](#the-boolean-data-type)
  - [The PLS_INTEGER Data Type](#the-pls_integer-data-type)
- [Working with Subtypes in PL/SQL](#working-with-subtypes-in-plsql)

<a name="understanding-basic-data-types"></a>
## Understanding Basic Data Types

All data types present in Oracle tables can also be utilized in PL/SQL, with a few minor exceptions mainly related to the maximum length. For instance, while a `CHAR` column in a table can store up to 2000 bytes, a `CHAR` variable in PL/SQL can accommodate up to 32767 bytes. Similarly, a `VARCHAR2` column's default maximum size is 4000 bytes, but this limit can be extended to 32767 bytes by setting the `MAX_SRING_SIZE` parameter. 

```sql
DECLARE
  char_var CHAR(32767);
  varchar2_var VARCHAR2(32767);
BEGIN
  -- Some PL/SQL code here
END;
```

<a name="working-with-large-objects-blobs-and-clobs"></a>
## Working with Large Objects: BLOBs and CLOBs

Large objects such as BLOBs (Binary Large Objects) and CLOBs (Character Large Objects) can store up to 128 terabytes in PL/SQL. However, when stored in a database table, their maximum size is limited based on the database block size. 

For instance, let's create a table named `whitepapers` with an `id` column and a `CLOB` column to store the text of the whitepaper.

```sql
CREATE TABLE whitepapers (
  id NUMBER,
  whitepaper_text CLOB
);
```

You can then insert a `CLOB` using a text string in a SQL statement:

```sql
INSERT INTO whitepapers VALUES (1, 'This is some example text for the whitepaper.');
COMMIT;
```

If you need to populate a very large `CLOB`, perhaps up to 128 terabytes, you must use PL/SQL and call procedures in the `DBMS_LOB` package.

```sql
DECLARE
  lob_loc CLOB;
  str VARCHAR2(32767) := 'This is some example text for the whitepaper.';
BEGIN
  INSERT INTO whitepapers VALUES (1, EMPTY_CLOB())
  RETURNING whitepaper_text INTO lob_loc;

  DBMS_LOB.APPEND(lob_loc, str);

  COMMIT;
END;
```

<a name="using-plsql-exclusive-data-types"></a>
## Using PL/SQL Exclusive Data Types

### The BOOLEAN Data Type

BOOLEAN is a PL/SQL exclusive data type that represents true or false values, proving handy for `IF` statements and `WHILE` loops. A `BOOLEAN` can have three values: true, false, or NULL. 

```sql
DECLARE
  bool_var BOOLEAN := true;
BEGIN
  IF bool_var THEN
    dbms_output.put_line('The variable is true');
  ELSE 
    dbms_output.put_line('The variable is false or null');
  END IF;
END;
```

To present a `BOOLEAN` value in a more readable way outside of PL/SQL, you can map it using a PL/SQL function:

```sql
CREATE OR REPLACE FUNCTION id_boolean(bool BOOLEAN) RETURN VARCHAR2 IS
BEGIN
  RETURN CASE bool
           WHEN TRUE THEN 'TRUE'
           WHEN FALSE THEN 'FALSE'
           ELSE 'NULL'
         END;
END;
```

### The PLS_INTEGER Data Type

`PLS_INTEGER` is another PL/SQL exclusive data type that efficiently stores integers within the range of approximately negative to positive 2 billion. It uses only four bytes of memory compared to the 20 or more bytes required to store the biggest `NUMBER`. Also, integer arithmetic operations performed on `PLS_INTEGER` are faster, taking better advantage of the database server hardware. 

```sql
DECLARE
  int_var PLS_INTEGER := 100;
  num_var NUMBER;
BEGIN
  num_var := int_var;
  num_var := num_var + 1;
END;
```

<a name="working-with-subtypes-in-plsql"></a>
## Working with Subtypes in PL/SQL

Subtypes in PL/SQL provide a means of creating more precise coding by adding restrictions on data types. They enforce business rules and ensure data integrity. For `PLS_INTEGER`, there are seven subtypes. Among these, `SIGNTYPE` stands out as it can only have three values: -1, 0, and 1, representing three-state logic.

```sql
DECLARE
  sig SIGNTYPE := 0;
BEGIN
  sig := 2; -- This will raise an error
END;
```

In summary, PL/SQL not only supports data types available in SQL but also introduces unique data types such as `BOOLEAN` and `PLS_INTEGER`, and enhances data manipulation using subtypes. This results in more efficient code, fewer errors, and increased data integrity.
