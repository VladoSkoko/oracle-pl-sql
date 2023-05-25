# Understanding and Implementing Error Handling in PL/SQL

Error handling is a crucial component in the development of any enterprise application. In the context of PL/SQL, understanding how to effectively utilize exceptions can make the difference between a seamless user experience and a frustrating one. This guide walks you through the fundamentals of error handling in PL/SQL, from simple exceptions to more complex user-defined exceptions, complete with real-world examples.

## Table of Contents
- [Handling Errors with EXCEPTION](#handling-errors-with-exception)
- [Different Types of Exceptions](#different-types-of-exceptions)
- [User-Defined Exception Types](#user-defined-exception-types)

---

## Handling Errors with EXCEPTION <a name="handling-errors-with-exception"></a>

Not all code runs perfectly all the time. Unknown parameters may be passed to your program, or your PL/SQL variable might contain a table name that doesn't exist. Using an exception block can help make block terminations more graceful and useful. 

Consider the following example:

```sql
BEGIN
  -- Main code block
  -- Something goes wrong here
EXCEPTION
  WHEN OTHERS THEN
    -- Error handling
END;
```

Here, any errors in the main code block are handled in the exception handler. It's always optional, but if used, it must be the last part of any PL/SQL block. Once an error occurs, control is transferred to the code in the exception block.

## Different Types of Exceptions <a name="different-types-of-exceptions"></a>

There are three types of exceptions in PL/SQL: internal, predefined, and user-defined.

1. **Internal Exceptions**: These are exceptions raised by Oracle itself. They are represented by negative error codes that start with 'ORA-'.

   For example, if you want to catch an 'ORA-00942: table or view does not exist' error, you can do this:

    ```sql
    DECLARE
      e_table_missing EXCEPTION;
      PRAGMA EXCEPTION_INIT(e_table_missing, -00942);
    BEGIN
      -- Attempt to access a non-existing table
    EXCEPTION
      WHEN e_table_missing THEN
        -- Handle missing table
      WHEN OTHERS THEN
        -- Handle other exceptions
    END;
    ```
   
2. **Predefined Exceptions**: These are common errors that Oracle has already assigned labels to, like `NO_DATA_FOUND` or `INVALID_NUMBER`.

   Here's how you can handle a `NO_DATA_FOUND` exception:

    ```sql
    BEGIN
      -- Code that might raise NO_DATA_FOUND
    EXCEPTION
      WHEN NO_DATA_FOUND THEN
        -- Handle exception
    END;
    ```

3. **User-Defined Exceptions**: These are exceptions that you define and raise manually in your program. They are particularly useful when you need to enforce specific business rules or conditions.

## User-Defined Exception Types <a name="user-defined-exception-types"></a>

User-defined exceptions are incredibly useful when the existing exceptions aren't sufficient to handle errors in your business logic.

For instance, consider the following code:

```sql
DECLARE
  e_bad_count EXCEPTION;
  employee_count NUMBER;
BEGIN
  SELECT COUNT(*) INTO employee_count FROM employees;
  IF employee_count < 110 THEN
    RAISE e_bad_count;
  END IF;
  -- Further processing
EXCEPTION
  WHEN e_bad_count THEN
    -- Handle low employee count
  WHEN OTHERS THEN
    -- Handle other exceptions
END;
```

In the example above, `e_bad_count` is a user-defined exception. The `RAISE` command is used to manually trigger this exception when the count of employees falls below 110. This exception is then handled in the exception block, which allows us to centralize our error handling.

Remember, although exceptions are optional, it is a good practice to have an exception handler at least at the calling procedure level, which can handle any unforeseen errors that might occur during program execution. The use of `SQLCODE` and `SQLERRM` can also help in identifying and handling unknown errors.

```sql
BEGIN
  -- Code that might raise an exception
EXCEPTION
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('Error code: ' || SQLCODE || ', message: ' || SQLERRM);
END;
```

In the code above, `SQLCODE` returns the error code and `SQLERRM` returns the error message associated with the most recently raised exception. These built-in functions are useful for handling exceptions that you did not anticipate.

By making use of these techniques, you can ensure that your PL/SQL code is robust, maintainable, and able to handle any errors that might occur.
