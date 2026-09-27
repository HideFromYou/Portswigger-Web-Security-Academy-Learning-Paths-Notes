# Clickjacking with CSRF Token

## Overview

Clickjacking can still be possible when the target application uses CSRF tokens.

A CSRF token protects the server against forged requests that do not contain a valid token. However, clickjacking works differently: the attacker frames the legitimate target page, allowing the victim's browser to interact with the real application and its valid form.

Therefore:

    Legitimate target page
            ↓
    Valid CSRF token
            ↓
    Transparent iframe
            ↓
    Victim clicks target action
            ↓
    Legitimate request is submitted

The presence of a CSRF token does not by itself prevent clickjacking.

---

## Why the CSRF Token Does Not Stop the Attack

Consider a legitimate form:

    <form action="/my-account/delete" method="POST">
        <input type="hidden" name="csrf" value="VALID-TOKEN">
        <button type="submit">Delete account</button>
    </form>

The token is already present in the legitimate page.

When that page is loaded inside the iframe, the browser can submit the form normally when the victim interacts with the target button.

The attacker does not need to know or manually create the CSRF token.

---

## Attack Structure

The attacker creates a page containing:

    [ Transparent target iframe ]
    [ Visible decoy element    ]

The decoy encourages the victim to click while the real target button is positioned underneath it.

Example:

    <div id="decoy">Click me</div>

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/my-account">
    </iframe>

The important point is that the iframe contains the **real authenticated page**.

---

## Basic Lab Methodology

### 1. Authenticate

Log in to the target application using the credentials provided by the lab.

    Username: wiener
    Password: peter

Navigate to the account page.

---

### 2. Identify the Sensitive Action

Inspect the account page and identify the action that should be triggered.

For example:

    Delete account

Inspect the HTML and identify the form and its CSRF token.

Example:

    <form action="/my-account/delete" method="POST">
        <input type="hidden" name="csrf" value="...">
        <button type="submit">Delete account</button>
    </form>

---

### 3. Create the iframe

Load the legitimate account page inside the exploit page:

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/my-account">
    </iframe>

---

### 4. Create the Decoy

Add a visible element that the victim will believe is the action they are supposed to click.

Example:

    <div id="decoy">Test me</div>

Use CSS positioning to align it with the real target button.

---

### 5. Test the Alignment

Initially use:

    opacity: 0.1;

This allows the target page to remain visible while positioning the decoy.

Check that the cursor changes to a hand when hovering over the decoy.

This indicates that the underlying target element is positioned correctly.

---

### 6. Make the iframe Invisible

After the alignment is correct, use:

    opacity: 0.0001;

The victim should now see the decoy rather than the target interface.

---

### 7. Deliver the Exploit

Change the decoy text:

    Test me

to:

    Click me

Then store the exploit and use:

    Deliver exploit to victim

The simulated victim performs the click.

---

## Important Lesson

The attack does not bypass the CSRF token.

Instead, the attack causes the victim's browser to interact with the **legitimate application**.

Therefore:

    Clickjacking
         ↓
    Legitimate page
         ↓
    Legitimate form
         ↓
    Valid CSRF token
         ↓
    Legitimate request

This is why CSRF protection and clickjacking protection address different problems.

---

## Key Takeaways

- CSRF tokens protect against forged cross-site requests.
- Clickjacking abuses the legitimate interface through an iframe.
- A legitimate framed page can contain a valid CSRF token.
- The attacker does not need to know the token.
- The victim's browser submits the legitimate form.
- CSRF protection alone is therefore not sufficient to prevent clickjacking.
- Framing restrictions such as `X-Frame-Options` and CSP `frame-ancestors` address the underlying iframe-based attack.

## Lab Status

Completed