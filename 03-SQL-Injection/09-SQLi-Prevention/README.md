# SQL Injection Prevention

## Overview

The main way to prevent SQL injection is to use parameterized queries instead of building SQL queries by concatenating user input.

The key principle is:

    User Input = Data
    SQL Query  = Code

User input should never be treated as part of the SQL query structure.

---

## Vulnerable Query

A vulnerable application may construct a query by concatenating user input:

    String query = "SELECT * FROM products WHERE category = '" + input + "'";

In this situation, the value of `input` becomes part of the SQL statement.

An attacker may therefore be able to modify the query's logic.

---

## Parameterized Queries

A safer approach is to use a parameterized query:

    PreparedStatement statement = connection.prepareStatement(
        "SELECT * FROM products WHERE category = ?"
    );

    statement.setString(1, input);

The SQL query structure is defined separately from the user-controlled value.

The input is treated as data rather than executable SQL syntax.

---

## Why Parameterized Queries Prevent SQL Injection

With string concatenation:

    SQL Query + User Input
            ↓
    User input becomes part of SQL syntax

With parameterization:

    SQL Query
       +
    Parameter
       ↓
    User input remains data

This prevents SQL metacharacters in the parameter value from changing the structure of the query.

---

## Where Parameterized Queries Should Be Used

Parameterized queries should be used for untrusted input used as values in SQL statements, including:

- `WHERE` values
- `INSERT` values
- `UPDATE` values
- Other user-controlled query parameters

Example:

    SELECT * FROM users WHERE username = ?

The username is supplied separately as a parameter.

---

## Table and Column Names

Parameterized queries cannot normally be used to directly parameterize SQL identifiers such as:

- Table names
- Column names
- `ORDER BY` identifiers

For these cases, applications should use techniques such as allowlists.

Example concept:

    User Input
        ↓
    Check against allowed values
        ↓
    Use only an approved identifier

---

## Important Rule

The SQL query string should be a hard-coded constant.

Do not build the query by concatenating variables containing user-controlled data.

Avoid:

    "SELECT * FROM users WHERE username = '" + username + "'"

Prefer:

    "SELECT * FROM users WHERE username = ?"

with the username supplied separately as a parameter.

---

## Practical Prevention Workflow

    1. Identify every place where user input reaches SQL
            ↓
    2. Use parameterized queries for values
            ↓
    3. Avoid string concatenation
            ↓
    4. Use allowlists for identifiers
            ↓
    5. Keep SQL query structure separate from user input

---

## Key Takeaways

- Use parameterized queries / prepared statements.
- Never concatenate untrusted input directly into SQL queries.
- Treat user input as data, not SQL code.
- Parameterization works for values but not directly for table or column names.
- Use allowlists when user input must determine an SQL identifier.
- The SQL query structure should remain fixed and separate from user-controlled data.