# Leveraging User-Defined Types in Pipelined Table Functions in Oracle

Table functions are a powerful feature of Oracle's PL/SQL language that can enhance the efficiency and scalability of data processing tasks. These functions can directly process and transform data in a more streamlined way compared to traditional batch processing methods. With this guide, we delve into the creation and usage of table functions, particularly pipelined table functions that allow for parallel execution and reduce the need for intermediary storage. We'll also be utilizing user-defined types, enabling us to handle more complex data.

## Table of Contents
1. [Traditional vs. Table Functions](#Traditional-vs.-Table-Functions)
2. [Pipelined Table Functions](#Pipelined-Table-Functions)
3. [User-Defined Types](#User-Defined-Types)
4. [Implementing a Pipelined Table Function](#Implementing-a-Pipelined-Table-Function)
5. [Querying the Pipelined Table Function](#Querying-the-Pipelined-Table-Function)
6. [Conclusion](#Conclusion)

### Traditional vs. Table Functions
The conventional way of processing transaction data often involves storing transactions in an intermediate table, transforming them in batches, and then storing them again in another location. This approach, while straightforward, presents several limitations:

1. **Storage Space**: Additional space is required for intermediate storage, which can become substantial during high-activity periods.
2. **Single-threaded Processing**: The batch process is generally single-threaded and thus, not very scalable.
3. **Unused CPUs**: If your database servers have additional CPUs, they're not being utilized to their full capacity with this approach.

### Pipelined Table Functions
Oracle's table functions, especially when declared as pipelined, present a more efficient solution. A pipelined table function can retrieve rows directly from the data source and send them to a load procedure, eliminating the need for an intermediate staging table. This function can also run in parallel, making it more scalable and responsive. 

However, without pipelining, you would have to wait until the table finished processing for results, and the storage space for the table would simply be switched for storage space in memory. Pipelined tables keep the memory and storage footprint low.

### User-Defined Types
To create a pipelined table function, you first need to create two user-defined types - one for the row and one for the table. These types will define the structure of the data that the function will process and return.

Here's how you can define these types:

```sql
-- Defining the row type
CREATE TYPE t_obj_row AS OBJECT 
(
  object_name VARCHAR2(128), 
  object_type VARCHAR2(19), 
  created DATE, 
  extr_date DATE
);

-- Defining the table type
CREATE TYPE t_obj_table AS TABLE OF t_obj_row;
```

In this example, `t_obj_row` represents a row of the `dba_objects` table, including an extra `extr_date` field. `t_obj_table` is a collection of `t_obj_row`, which will be the data type returned by our pipelined function.

### Implementing a Pipelined Table Function
The pipelined table function should be declared in a manner different from a regular function returning a single value. It must include the PIPELINED keyword, and the return type should be a table type. The PIPE ROW statement is then used to pass rows to the caller.

Here's an example:

```sql
CREATE OR REPLACE FUNCTION trans_oltp
RETURN t_obj_table PIPELINED 
AS
BEGIN
  FOR r IN (SELECT object_name, object_type, created FROM dba_objects WHERE created > SYSDATE - 10) LOOP
    PIPE ROW(t_obj_row(r.object_name, r.object_type, r.created, SYSDATE));
  END LOOP;
  RETURN;
END;
```

In this function, we're iterating over the rows of `dba_objects` created within the last 10 days. For each row, we're using the `PIPE ROW` statement to pass a `t_obj_row` instance containing the row's data and the current date as `extr_date`.

### Querying the Pipelined Table Function
One of the key benefits of table functions is that they can be queried like regular tables. The calling statement does not need to be aware that it's calling a function, as it appears like a physical table.

Here's how you can query our example pipelined table function:

```sql
SELECT * FROM TABLE(trans_oltp);
```

This query would return a set of rows containing object data from the `dba_objects` table, similar to what a query on a physical table would return.

### Conclusion
Pipelined table functions extend the capabilities of traditional table functions by enabling immediate return of results, making them an excellent tool for enhancing the performance of your PL/SQL procedures. Combined with user-defined types, you can handle complex data structures and datasets in a more efficient and scalable manner. However, the benefits don't stop there - these functions also reduce the need for intermediate storage, thereby conserving memory and storage resources.
