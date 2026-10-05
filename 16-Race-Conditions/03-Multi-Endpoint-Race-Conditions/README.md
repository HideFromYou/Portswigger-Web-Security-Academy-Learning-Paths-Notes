# Multi-Endpoint Race Conditions

## Overview

In a multi-endpoint race condition, the requests are sent to different endpoints, but they affect the same underlying state.

Because the endpoints are different, they are processed by different code and may take different amounts of time.

## How It Works

Example: two endpoints that both touch the same order.

/payment/confirm
/add-item

One request confirms the payment for the order. The other adds an item to the same order.

If the item is added after the application has validated the order but before the order is finalized, the final order may contain something that was never validated.

## Aligning Race Windows

Network delays and endpoint-specific processing times can make parallel requests arrive out of alignment, so the race window is missed.

### Connection Warming

Send a benign request first to warm the connection.

This helps to rule out back-end network delay as the cause of timing drift between the requests.

### Abusing Rate or Resource Limits

Deliberately trigger a server-side rate limit to introduce a consistent, predictable delay.

This can make the single-packet technique usable even when the endpoint needs some extra delay to line up.

## Practical Lab

Lab: Multi-endpoint race conditions

The goal was to purchase an item that cost more than the available store credit.

The application had a race between `/cart/checkout` and `/cart`:

- The checkout request validated the cart against the store credit.
- A second request could change the cart before the order was confirmed.

The attack:

1. Add an item that can be afforded to the cart.
2. Send the checkout request and the request that adds the expensive item in parallel.
3. The expensive item was added while the cheap item's checkout was still being validated and confirmed.

The order was confirmed with the expensive item, even though the store credit was not enough.

Status: Solved

## Testing Process

1. Find two endpoints that act on the same object, such as a cart or an order.
2. Identify which request validates the state and which one changes it.
3. Warm the connection with a benign request.
4. Send both requests in parallel.
5. If the window is missed, adjust the timing and repeat.
6. Check whether the final state contains something that was never validated.

## Key Takeaway

Different endpoints can share the same state.

If validation and confirmation are separate steps, another request can change the state in between.
