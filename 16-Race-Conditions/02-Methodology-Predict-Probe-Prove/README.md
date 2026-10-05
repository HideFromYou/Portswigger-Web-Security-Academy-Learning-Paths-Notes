# Methodology - Predict, Probe, Prove

## Overview

Race conditions are difficult to find by guessing. A structured approach avoids wasting time on endpoints that cannot collide.

The methodology has three phases: Predict, Probe, and Prove.

## 1. Predict

Identify security-critical endpoints that have collision potential.

A collision is possible when two or more requests can affect the same underlying state or record.

Typical examples are:

- Email change
- Password reset
- Checkout and payment
- Coupon or gift card redemption
- Account or object creation

## 2. Probe

First benchmark the normal behavior of the application by sending the requests one after another.

Then send the same requests in parallel and look for deviations from the baseline, such as:

- A different status code
- A different response body
- Second-order effects, for example a different email, a changed value later on, or an unexpected state in the account

In Burp Suite Repeater the requests can be placed in a group and sent first in sequence and then in parallel.

## 3. Prove

When a deviation is found:

- Isolate the minimal set of requests needed to cause it
- Repeat the attack
- Confirm that the result is reproducible

Only a reproducible result can be considered a confirmed vulnerability.

## Testing Process

Identify endpoints with collision potential
        ↓
Benchmark the sequential behavior
        ↓
Send the requests in parallel
        ↓
Compare the results with the baseline
        ↓
Isolate the minimal set of requests
        ↓
Repeat to confirm it is reproducible

## Key Takeaway

Always compare the parallel behavior against a sequential baseline.

A race condition is only proven when the deviation is isolated and can be reproduced.
