# Null Byte Bypass

## Overview

A null byte can sometimes be used to bypass file extension validation in a Path Traversal vulnerability.

This is useful when the application requires the user-supplied filename to end with a specific extension, such as `.jpg` or `.png`.

## Example

If the application requires a `.jpg` extension, a normal traversal attempt may be rejected:

../../../etc/passwd

The null byte can be placed before the required extension:

../../../etc/passwd%00.jpg

The `%00` represents a null byte.

The idea is that the null byte can terminate the path before the `.jpg` extension is processed by the component handling the filesystem path.

## Practical Lab

In the PortSwigger lab, the application validated that the supplied filename ended with the expected image extension.

We used:

GET /image?filename=../../../etc/passwd%00.jpg

The server returned the contents of `/etc/passwd`, confirming that the extension validation had been bypassed.

## Testing

When an application requires a specific file extension, test:

../../../etc/passwd%00.jpg

For a PNG requirement:

../../../etc/passwd%00.png

## Key Takeaway

The important concept is the position of the null byte.

It is placed after the target filename and before the extension required by the application:

../../../etc/passwd%00.jpg
                  ^
              null byte

This can bypass certain filename extension validation mechanisms.