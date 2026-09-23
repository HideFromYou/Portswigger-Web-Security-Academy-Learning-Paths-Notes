# Absolute Path

## Overview

An absolute path specifies the complete location of a file from the filesystem root.

When an application accepts user-controlled file paths without properly restricting them, an attacker may be able to provide an absolute path instead of using path traversal sequences.

## Example

Linux:

GET /image?filename=/etc/passwd

The application is instructed to access:

/etc/passwd

Windows:

GET /image?filename=C:\Windows\win.ini

The application is instructed to access:

C:\Windows\win.ini

## Practical Testing

When a parameter appears to reference a file, test whether the application accepts an absolute path.

Linux example:

/etc/passwd

Windows example:

C:\Windows\win.ini

If the application accepts the absolute path and returns the requested file, this indicates that the application is allowing user-controlled filesystem locations.

## Key Takeaway

Absolute path testing is useful when an application directly accepts filesystem paths.

The main question is whether the supplied path is restricted to the application's intended directory or can reference arbitrary locations on the filesystem.