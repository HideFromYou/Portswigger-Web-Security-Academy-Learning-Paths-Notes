# Multistep Clickjacking

## Overview

Multistep clickjacking is a clickjacking attack that requires the victim to perform multiple actions in a specific sequence.

Unlike basic clickjacking, where one click triggers the target action, multistep clickjacking involves different target actions across multiple pages or application states.

---

## Basic Concept

Example:

    Click me first
          ↓
    Delete account
          ↓
    Confirmation page
          ↓
    Click me next
          ↓
          Yes

The first decoy is aligned with the initial target action.

After the first click, the target application changes state or loads a new page.

The second decoy must then be aligned with the target action on the new page.

---

## Why Two Decoy Elements Are Required

The two actions exist at different stages of the application.

Example:

    .firstClick
        ↓
    Delete account

    .secondClick
        ↓
    Yes

The first and second decoys therefore require different positions.

Example structure:

    <div class="firstClick">Click me first</div>
    <div class="secondClick">Click me next</div>

The CSS can position each element independently.

---

## Attack Flow

    Attacker exploit page
            ↓
    Target account page in iframe
            ↓
    "Click me first"
            ↓
    Delete account
            ↓
    Confirmation page
            ↓
    "Click me next"
            ↓
    Yes
            ↓
    Target action completed

The attacker must account for the changing state of the framed page.

---

# Lab Methodology

## 1. Identify the First Action

Inspect the target account page and identify the initial sensitive action.

In the lab, the first action is:

    Delete account

The legitimate page contains a form protected by a CSRF token.

---

## 2. Identify the Confirmation Step

After the first action, the application displays a confirmation page.

Example:

    Are you sure?

The confirmation page contains a second action:

    Yes

This is the second click target.

---

## 3. Create Two Decoys

The exploit page contains two visible elements:

    <div class="firstClick">Test me first</div>
    <div class="secondClick">Test me next</div>

Each decoy represents a different stage of the attack.

---

## 4. Position the First Decoy

The first decoy must align with the initial target:

    Click me first
          ↓
    Delete account

Initially use a visible iframe opacity:

    opacity: 0.1;

Adjust the `top` and `left` values until the first decoy is correctly aligned.

---

## 5. Trigger the First State Change

When the first target is clicked, the iframe navigates to the confirmation page.

The important point is that the iframe content has now changed.

The second decoy must therefore correspond to the new page.

---

## 6. Position the Second Decoy

The second decoy must align with the confirmation action:

    Click me next
          ↓
          Yes

The position is independent from the first decoy.

Example:

    .firstClick {
        top: 510px;
        left: 50px;
    }

    .secondClick {
        top: 430px;
        left: 70px;
    }

The exact values depend on the target page and browser viewport.

---

## 7. Test the Alignment

Use:

    opacity: 0.1;

while positioning the elements.

First verify:

    Click me first → Delete account

Then verify:

    Click me next → Yes

The hand cursor can be used to confirm that the underlying target action is correctly positioned.

---

## 8. Make the iframe Transparent

After both targets are correctly aligned:

    opacity: 0.0001;

The victim should see only the decoy elements.

Change:

    Test me first
    Test me next

to:

    Click me first
    Click me next

---

## 9. Deliver the Exploit

Store the exploit and use:

    Deliver exploit to victim

The simulated victim performs the clicks in sequence.

---

# Important Concept

The most important difference from basic clickjacking is that the attacker must understand the **state transition** of the target application.

Basic clickjacking:

    Decoy
      ↓
    One target action

Multistep clickjacking:

    Decoy 1
      ↓
    Target action 1
      ↓
    New page/state
      ↓
    Decoy 2
      ↓
    Target action 2

The second click is therefore not aligned with the first page's button.

---

# Pentester Mindset

When testing for multistep clickjacking, ask:

1. What happens after the first click?
2. Does the target navigate to another page?
3. Does a confirmation dialog or confirmation page appear?
4. What is the next actionable element?
5. Can the second action also be aligned with attacker-controlled content?
6. Does the complete sequence result in a sensitive action?

Think in terms of:

    Action 1 → State change → Action 2 → Result

---

## Key Takeaways

- Multistep clickjacking requires multiple victim interactions.
- Different decoys can target different application states.
- The first click may cause the iframe to navigate to another page.
- The second decoy must be aligned with the target action on that new page.
- Each click target may require different `top` and `left` values.
- Testing should verify each step separately.
- The complete attack depends on the sequence of application states.

## Lab Status

Completed