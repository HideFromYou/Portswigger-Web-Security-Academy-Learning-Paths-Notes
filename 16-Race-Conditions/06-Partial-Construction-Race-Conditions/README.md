# Partial Construction Race Conditions

## Overview

Some objects are created in more than one step. For example, a user row is inserted first and an API key is set later by a separate statement.

For a short time the object exists, but one of its fields is uninitialized (null or empty).

## How It Works

Insert user row
        ↓
        (window: the API key is null or empty)
        ↓
Set the API key in a separate statement

If a request is processed during that window, the application compares the submitted value with an uninitialized value.

## Exploitation

The attack is to submit an input that matches the uninitialized value in a loose comparison.

### PHP

param[]=foo   →   param = ['foo']
param[]       →   param = []

### Ruby on Rails

param[key]    →   params = {"param"=>{"key"=>nil}}

An empty array or a nil value can match a null or empty field, so the check may pass even though no real value was provided.

## Testing Process

1. Find objects that are created in several steps, such as registration or API key creation.
2. Identify the field that is filled in later.
3. Send a request that uses the new object while it is being created.
4. Use input that matches the uninitialized value, such as an empty array or a nil value.
5. Repeat the request many times to hit the window.

## Key Takeaway

Objects created in several steps are temporarily incomplete.

An input that matches the uninitialized value can bypass a comparison during that window.
