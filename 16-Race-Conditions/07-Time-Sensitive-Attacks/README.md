# Time-Sensitive Attacks

## Overview

Time-sensitive attacks are not a true race condition, but they use the same technique of sending requests at the same moment.

They exploit broken cryptography: a security token is derived, at least partly, from a high-resolution timestamp instead of a cryptographically secure random value.

## How It Works

If two requests are processed at the same timestamp, the tokens generated from that timestamp can be identical.

An attacker can therefore time two requests so that the application generates the same token for two different users.

## Practical Lab

Lab: Exploiting time-sensitive vulnerabilities

The password reset token was a hash that incorporated a timestamp.

The attack:

1. The application locked requests per session, so the requests were sent from two different sessions.
2. Both requests were sent in parallel and repeated until they landed on the same timestamp. This was confirmed when the tokens were identical.
3. A password reset for `wiener` was raced against a password reset for `carlos`.
4. The shared token was captured through the attacker's own inbox.
5. The token was used to reset the password of the `carlos` account and take it over.

Status: Solved

## Testing Process

1. Request two tokens one after another and compare their format.
2. Check whether the tokens change with time or look derived from a timestamp.
3. If requests are locked per session, use a different session for each request.
4. Send the requests in parallel until two tokens are identical.
5. Use the shared token to attack another account.

## Key Takeaway

A token that depends on a timestamp can be predicted or duplicated.

Security tokens must come from a cryptographically secure random generator, not from the time.
