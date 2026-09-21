# Second-Order SQL Injection

## Overview

Second-order SQL injection occurs when malicious input is first stored by the application and is later retrieved and used unsafely in a different SQL query.

Unlike first-order SQL injection, the payload does not necessarily execute immediately when it is submitted.

The basic flow is:

    Attacker Input
          ↓
    Application stores the input
          ↓
    Input is retrieved later
          ↓
    Retrieved value is used in another SQL query
          ↓
    SQL Injection occurs

---

## First-Order vs Second-Order SQL Injection

### First-Order SQL Injection

The injected input is immediately used in a vulnerable SQL query.

    User Input
        ↓
    SQL Query
        ↓
    Database

### Second-Order SQL Injection

The input is stored first and executed later.

    User Input
        ↓
    Database Storage
        ↓
    Later Retrieval
        ↓
    Vulnerable SQL Query
        ↓
    Database

---

## Why Second-Order SQL Injection Happens

A common mistake is assuming that data stored in the database is automatically trusted.

For example:

    1. Application safely stores user input.
    2. The value is later retrieved from the database.
    3. The developer assumes the stored value is trusted.
    4. The value is concatenated into a new SQL query.
    5. SQL injection occurs.

The important point is:

    Stored data can still be attacker-controlled data.

---

## Testing Approach

When testing for second-order SQL injection, look for situations where:

    Input → Stored → Retrieved → Used in SQL

Potential locations include:

- Usernames
- Profile information
- Comments
- Account settings
- Stored preferences
- Other database fields controlled by the user

The important question is:

    Is my input stored somewhere and later inserted into another SQL query?

---

## Practical Workflow

    1. Identify an input that is stored by the application
            ↓
    2. Submit a controlled SQL injection payload
            ↓
    3. Confirm that the value is stored
            ↓
    4. Identify where the stored value is used later
            ↓
    5. Observe whether it reaches another SQL query
            ↓
    6. Test whether the stored value changes the query's behavior

---

## Key Takeaways

- Second-order SQL injection occurs after attacker-controlled data has been stored.
- The payload may not execute when it is initially submitted.
- Stored data should not automatically be considered trusted.
- Testing requires tracing the lifecycle of the input:
  
      Input → Storage → Retrieval → SQL Query

- The main difference from first-order SQL injection is when and where the malicious input is executed.