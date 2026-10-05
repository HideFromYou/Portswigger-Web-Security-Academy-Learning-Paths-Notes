# Hidden Multi-Step Sequences

## Overview

A single HTTP request can look like one operation from the outside, but internally the application often processes it in several steps.

Between those steps, the application is briefly in a temporary sub-state. If another request arrives during that window, it can be processed while the application is in a state the developer never intended.

## How It Works

Example of what may happen inside one login request:

Request received
        ↓
Session created and user ID stored      (sub-state: user is "logged in")
        ↓
MFA requirement enforced                (sub-state: MFA is now required)

The security control is applied in a later step than the one that creates the session.

Between the second and the third step the application is in an unintended sub-state: the user is logged in, but MFA is not yet enforced.

## Example

If a second request reaches a protected endpoint during that window, the application may treat the session as fully authenticated and process the request.

The race window can be very small, so the requests usually need to be sent in parallel and the attack needs to be repeated.

## Testing Process

1. Identify operations where one request may trigger several internal steps, such as login, registration, checkout, or account changes.
2. Look for security controls that are applied after the object or session already exists.
3. Send the main request together with a probing request in parallel.
4. Compare the result with the normal sequential behavior.
5. Look for responses that reveal an intermediate state.

## Key Takeaway

A request is not always a single atomic operation.

If the application changes state in several internal steps, there may be a moment where the object exists but its security controls are not yet in place.
