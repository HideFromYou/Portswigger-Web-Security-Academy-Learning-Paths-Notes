# Mass Assignment

## Overview

This section focused on mass assignment vulnerabilities in APIs.

The main goal was to understand how applications can automatically bind user-controlled request parameters to internal object properties, potentially allowing users to modify properties that were not intended to be directly controlled.

Topics covered:

- What mass assignment is
- Automatic parameter binding
- Identifying hidden parameters
- Comparing `GET` responses with `PATCH` requests
- Testing hidden parameters
- Exploiting a mass assignment vulnerability
- Using the vulnerability to modify a hidden discount parameter

---

## 1. What is Mass Assignment?

Mass assignment can occur when an application automatically takes parameters supplied by the user and assigns them to properties of an internal object.

For example, an application may expect the user to update:

    {
      "username": "wiener"
    }

However, the server-side object may contain additional properties such as:

    {
      "username": "wiener",
      "role": "user",
      "email": "wiener@example.com"
    }

If the application automatically binds user-supplied parameters to object properties without properly restricting which properties can be modified, an attacker may attempt to modify sensitive properties.

---

## 2. Identifying Hidden Parameters

One method for identifying possible mass assignment vulnerabilities is to compare the parameters sent by the client with the fields returned by the API.

For example, a `PATCH` request might contain:

    {
      "username": "wiener"
    }

while a `GET` request may return:

    {
      "username": "wiener",
      "email": "wiener@example.com",
      "chosen_discount": {
        "percentage": 0
      }
    }

The additional fields returned by the API can be candidates for further testing.

### Testing Method

The process is:

    Send normal request
          ↓
    Inspect API response
          ↓
    Identify additional fields
          ↓
    Select a possible hidden parameter
          ↓
    Add it to the request
          ↓
    Analyze the response
          ↓
    Test whether the parameter can be modified

---

## 3. Testing Hidden Parameters

After identifying a possible hidden parameter, it can be tested by adding it to a request.

Both valid and invalid values can be useful.

For example:

    {
      "chosen_discount": {
        "percentage": 10
      }
    }

If the server processes the parameter and returns a different response, this can indicate that the property is being accepted.

Testing invalid values can also reveal whether the application is processing the parameter.

---

## 4. Mass Assignment Lab

### Objective

The objective of the lab was to exploit a mass assignment vulnerability and purchase a product using a hidden discount parameter.

I first logged in using:

    wiener:peter

I then added the required product and inspected the checkout functionality.

During the investigation, I compared the API data with the parameters normally sent by the client.

The hidden parameter identified was:

    chosen_discount

The parameter contained:

    {
      "percentage": 10
    }

---

## 5. Testing the Hidden Discount

I first tested a discount of `10` percent.

Request data:

    {
      "chosen_discount": {
        "percentage": 10
      }
    }

The parameter was accepted, but the discount was not sufficient to complete the purchase because there were insufficient funds.

This confirmed that the hidden parameter was being processed by the application.

---

## 6. Exploiting the Vulnerability

I then changed the discount to `100` percent.

Request:

    {
      "chosen_discount": {
        "percentage": 100
      }
    }

The server responded with:

    HTTP/2 201 Created

The response also provided a location indicating that the order had been successfully created:

    /cart/order-confirmation?order-confirmed=true

The purchase was successfully completed.

Status:

    SOLVED

---

## 7. Attack Flow

The attack methodology was:

    Login
      ↓
    Add product
      ↓
    Inspect checkout API
      ↓
    Compare request and response fields
      ↓
    Identify hidden parameter
      ↓
    Test chosen_discount
      ↓
    Test 10% discount
      ↓
    Parameter accepted
      ↓
    Test 100% discount
      ↓
    Order created
      ↓
    Lab solved

---

## 8. Pentester Mindset

When testing an API for mass assignment vulnerabilities, do not only focus on the parameters that the front-end normally sends.

Ask:

1. What fields are returned by the API?
2. Which fields are not normally included in client requests?
3. Could one of these fields be accepted by the server?
4. What happens if I add the field manually?
5. Does the server accept valid values?
6. Does the server behave differently with invalid values?
7. Can a sensitive property be modified?

The key idea is to compare what the client is allowed to send with what the server-side object actually contains.

---

## 9. Key Takeaways

- Mass assignment occurs when user-controlled parameters are automatically mapped to object properties.
- Sensitive properties may become modifiable if the application does not restrict them.
- Comparing `GET` responses with update requests can help identify hidden parameters.
- Hidden parameters should be tested with both valid and invalid values.
- A parameter being accepted does not automatically mean it is exploitable; its security impact must be verified.
- In the lab, the hidden `chosen_discount` property could be modified.
- Setting the discount to `100%` allowed the purchase to be completed.

---

## 10. Prevention

Applications should explicitly control which properties users are allowed to modify.

A safer approach is to use an allowlist of permitted properties.

For example, if a user should only be able to modify:

    username
    email

the application should not automatically allow properties such as:

    role
    isAdmin
    chosen_discount
    accountBalance

to be modified through the same request.

The main defensive principle is:

    Allowlist user-updatable properties
            +
    Protect sensitive properties
            =
    Reduced mass assignment risk