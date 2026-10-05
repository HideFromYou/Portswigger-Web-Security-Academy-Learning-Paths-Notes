# Race Condition Prevention

## Overview

Race conditions happen when a sensitive state change is split into several steps that can be interleaved with other requests.

Prevention focuses on removing the window between those steps.

## 1. Avoid Mixing Data From Different Storage Layers

Do not combine data from different storage layers, such as the session and the database, in the same decision.

Different layers are updated at different times, which creates windows.

## 2. Make Sensitive State Changes Atomic

Sensitive changes should happen as a single operation.

For example, use one database transaction for validating a payment and confirming an order.

## 3. Use Datastore Integrity Features

Use features such as uniqueness constraints as defense in depth.

The database can reject a duplicate even when the application logic fails to.

## 4. Do Not Enforce Limits in a Different Layer

Do not use one storage layer to enforce limits on another.

For example, do not use the session to enforce a limit on database records.

## 5. Update Session Variables as a Batch

Update session variables as a single batch, not one by one.

Transaction boundaries should be explicit and not hidden by an ORM.

## 6. Consider Avoiding Server-Side State

Avoiding server-side state entirely, for example by keeping the state client-side with JWTs, removes the shared state that races depend on.

Be aware of the separate risks that JWTs introduce.

## Secure Flow

Begin transaction
        ↓
Validate the state
        ↓
Apply the change
        ↓
Commit the transaction

No other request can see or change the state between validation and change.

## Key Takeaway

The fix is to make validation and change one atomic operation.

Integrity constraints and explicit transactions protect the application even when the logic has gaps.
