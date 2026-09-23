# Path Traversal Prevention

## Overview

The most effective way to prevent Path Traversal is to avoid passing user-supplied input to filesystem APIs whenever possible.

If user input must be used, the application should use multiple layers of defense.

## 1. Validate User Input

The preferred approach is to compare the supplied value against a whitelist of permitted values.

For example, instead of accepting arbitrary filenames:

profile.jpg
avatar.png
logo.png

the application only allows known, expected values.

If a whitelist is not possible, validate that the input contains only permitted content, such as alphanumeric characters.

## 2. Canonicalize the Path

After validating the input, append it to the expected base directory and canonicalize the resulting path using the platform filesystem API.

The application should then verify that the canonicalized path still starts with the expected base directory.

## Example

Java:

File file = new File(BASE_DIRECTORY, userInput);

if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // process file
}

The important check is that the final canonical path remains inside the expected base directory.

## Secure Flow

User Input
    ↓
Validate Input
    ↓
Append to Base Directory
    ↓
Canonicalize Path
    ↓
Verify Path Starts With Base Directory
    ↓
Process File

## Key Takeaway

Path Traversal prevention should not rely on blocking a single sequence such as `../`.

The application should validate the input and verify the canonical filesystem path to ensure that the requested file remains inside the intended directory.