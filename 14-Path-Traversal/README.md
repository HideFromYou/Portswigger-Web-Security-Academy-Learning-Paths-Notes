# Path Traversal

Path Traversal is a web vulnerability that allows an attacker to access files outside the intended directory of an application by manipulating user-controlled file or path parameters.

The main objective when testing for Path Traversal is to determine whether user input can influence a filesystem path and escape the application's intended directory.

## Techniques Covered

### 01 - Basic Path Traversal

Using traversal sequences such as `../` to move outside the application's intended directory and access files.

### 02 - Absolute Path

Using an absolute filesystem path when the application allows the supplied path to directly reference a file.

### 03 - Encoded Path Traversal

Using URL encoding or double URL encoding to bypass filters that block normal traversal sequences.

### 04 - Validation Bypass

Bypassing application validation that attempts to prevent Path Traversal by restricting or modifying the supplied filename.

### 05 - Null Byte Bypass

Using a null byte such as `%00` to bypass file extension validation when the application requires a specific extension.

### 06 - Path Traversal Prevention

Understanding how applications can prevent Path Traversal through input validation, whitelisting, path canonicalization, and directory validation.

## Testing Methodology

When a parameter appears to interact with the filesystem, the general testing process is:

Identify file/path parameter
        ↓
Test basic traversal
        ↓
Try alternative path representations
        ↓
Identify validation or filtering
        ↓
Test an appropriate bypass
        ↓
Verify access to a known file

## Common Parameters

Potentially interesting parameters include:

filename=
file=
path=
image=
document=
folder=

## Common Target Files

### Linux

/etc/passwd
/etc/hosts
/etc/hostname

### Windows

C:\Windows\win.ini
C:\Windows\System32\drivers\etc\hosts

## Labs

The techniques in this section were practiced through the Path Traversal labs in PortSwigger Web Security Academy.

The labs covered different obstacles such as input validation, encoded traversal sequences, filename restrictions, and null byte extension bypasses.

## Key Takeaways

- User-controlled input should not be trusted when constructing filesystem paths.
- Traversal sequences such as `../` can allow access outside the intended directory.
- Different encoding techniques may bypass weak input filters.
- Validation mechanisms can introduce additional obstacles that need to be tested.
- A null byte can bypass certain filename extension checks.
- Secure applications should validate user input and canonicalize filesystem paths before accessing files.