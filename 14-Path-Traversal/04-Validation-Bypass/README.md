# Validation Bypass

## Overview

Applications may try to prevent Path Traversal by validating or filtering the supplied filename.

These checks can sometimes be bypassed when the application only blocks specific traversal patterns or performs incomplete validation.

## Example

A simple validation rule may attempt to block:

../

An attacker can test alternative representations of the same traversal sequence, depending on how the application processes the input.

For example:

../
..\
%2e%2e%2f

## Practical Testing

When a normal traversal attempt is blocked:

../../../etc/passwd

test alternative representations:

..\..\..\etc\passwd

%2e%2e%2f%2e%2e%2fetc/passwd

Double URL encoding can also be tested when the application performs multiple decoding steps:

%252e%252e%252f%252e%252e%252fetc/passwd

## What to Look For

The goal is to understand how the application validates and processes the filename.

Test whether:

- `../` is blocked
- backslashes are handled differently
- URL-encoded characters are decoded
- double encoding is decoded
- the application normalizes the path before validation

## Key Takeaway

Path Traversal validation should account for how the application actually interprets and normalizes paths, not just block one literal string such as `../`.

When testing, first identify what the validation blocks and then determine whether an alternative representation is interpreted differently by the application.