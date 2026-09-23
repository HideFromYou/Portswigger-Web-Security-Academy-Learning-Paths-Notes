# Basic Path Traversal

## Overview

Path Traversal is a vulnerability that occurs when an application uses user-controlled input to construct a filesystem path without properly validating it.

An attacker can use traversal sequences such as `../` to move outside the intended directory and access files that should not be accessible.

## How It Works

A vulnerable application may use a parameter such as:

GET /image?filename=example.jpg

The application expects the filename to point to an image inside a specific directory.

By modifying the filename with traversal sequences, we can attempt to access files outside that directory.

## Example

Original request:

GET /image?filename=example.jpg

Path traversal attempt:

GET /image?filename=../../../etc/passwd

The sequence `../../../` moves up through parent directories until reaching the filesystem root.

The application then attempts to access:

/etc/passwd

## Practical Lab

In the PortSwigger lab, the application used a `filename` parameter to load product images.

The vulnerable request was:

GET /image?filename=...

We modified the parameter to:

../../../etc/passwd

The server returned the contents of `/etc/passwd`, confirming that the application allowed access to a file outside the intended image directory.

## Testing Process

When a parameter appears to reference a file, test basic traversal first:

../
../../
../../../

Then try accessing a known file:

../../../etc/passwd

For Windows targets:

..\..\..\Windows\win.ini

## Key Takeaway

The important concept is that the application trusted user-controlled path input.

If the application does not properly restrict the resulting filesystem path, `../` sequences can be used to escape the intended directory and access files outside the intended directory.