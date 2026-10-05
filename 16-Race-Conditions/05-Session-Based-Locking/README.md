# Session-Based Locking Mechanisms

## Overview

Some frameworks lock the session while a request is processed. For example, the native PHP session handler processes only one request per session at a time.

This can hide a race condition during testing, because the parallel requests are silently processed one after another.

## How It Works

Parallel requests with the same session token
        ↓
The first request locks the session
        ↓
The other requests wait until the lock is released
        ↓
The requests are processed sequentially

The race window disappears, even though the underlying race condition may still exist.

## Example

When parallel requests always seem to be processed one after another, with a consistent delay between the responses, the application may be locking the session.

## Workaround

Send each request using a different session token.

Different sessions are not locked by each other, so the requests can be processed in parallel again.

This was used in the time-sensitive lab, where the requests were sent from two different sessions.

## Testing Process

1. Send parallel requests with the same session and observe the timing of the responses.
2. If the responses seem to be processed one by one, suspect session locking.
3. Repeat the test with a different session token for each request.
4. Compare the behavior.

## Key Takeaway

A failed race condition test does not prove that the application is safe.

Session-based locking can hide the vulnerability, so use a different session for each request.
