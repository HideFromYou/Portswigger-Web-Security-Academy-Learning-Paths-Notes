# Race Conditions

Race conditions happen when an application processes requests concurrently and the outcome depends on the timing of those requests.

By sending requests at exactly the right moment, an attacker can interact with the application in a temporary state that the developer never intended.

The main objective when testing for race conditions is to find a short window between a security check and the action that depends on it, and to exploit it with requests that arrive at the same time.

## Techniques Covered

### 01 - Hidden Multi-Step Sequences

A single request may pass through several internal sub-states. Exploiting the window between them, for example "logged in" but "MFA not yet enforced", can bypass a security control.

### 02 - Methodology: Predict, Probe, Prove

A structured approach: identify endpoints with collision potential, compare sequential and parallel behavior, then isolate and reproduce the result.

### 03 - Multi-Endpoint Race Conditions

Requests to different endpoints that affect the same state, including connection warming and aligning the race windows.

### 04 - Single-Endpoint Race Conditions

Parallel requests to the same endpoint with different values, racing to write to shared state that is not updated atomically.

### 05 - Session-Based Locking

Frameworks that lock the session can hide a race condition. Each request needs a different session token.

### 06 - Partial Construction Race Conditions

Objects created in several steps have a window where a field is uninitialized and can be matched by a crafted input.

### 07 - Time-Sensitive Attacks

Tokens derived from a timestamp can be duplicated by timing two requests so that they get the same timestamp.

### 08 - Race Condition Prevention

Atomic state changes, integrity constraints, and avoiding mixed storage layers.

## Testing Methodology

When an endpoint changes security-critical state, the general testing process is:

Identify an endpoint with collision potential
        ↓
Benchmark the sequential behavior
        ↓
Send the requests in parallel
        ↓
Compare the results with the baseline
        ↓
Isolate the minimal set of requests
        ↓
Repeat to confirm the result is reproducible

## Labs

The techniques in this section were practiced through the Race Conditions labs in PortSwigger Web Security Academy.

- Multi-endpoint race conditions: Solved
- Single-endpoint race conditions: Solved
- Exploiting time-sensitive vulnerabilities: Solved

## Key Takeaways

- A single request is not always a single atomic operation.
- Always compare parallel behavior against a sequential baseline.
- Session-based locking can hide a race condition, so use a different session for each request.
- Objects created in several steps are temporarily incomplete.
- Tokens derived from a timestamp can be duplicated.
- Sensitive state changes should be atomic and protected by integrity constraints.
