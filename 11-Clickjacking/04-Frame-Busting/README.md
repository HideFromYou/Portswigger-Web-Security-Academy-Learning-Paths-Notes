# Frame Busting

## Overview

Frame busting is a client-side technique designed to prevent a web page from being loaded or remaining inside an iframe.

It is commonly implemented using JavaScript that checks whether the current page is the top-level browser window.

The basic idea is:

    Is my page the top-level window?
              ↓
        Yes → continue
        No  → break out of the iframe

---

## What Frame-Busting Scripts Try to Do

A frame-busting script may:

- Check whether the page is running inside an iframe.
- Force the page to become the top-level window.
- Redirect the browser away from the framed page.
- Prevent interaction with content when it detects framing.

Because this protection relies on client-side JavaScript and browser behavior, it can sometimes be bypassed.

---

## Why Frame Busting Is Not a Complete Defense

Frame busting runs on the client side.

The browser controls whether JavaScript executes and what restrictions are applied to the framed document.

Therefore, an attacker may look for ways to restrict the behavior of the framed page so that its frame-busting code cannot successfully escape the iframe.

One technique covered in this section is the HTML5 `sandbox` attribute.

---

## iframe Sandbox

The `sandbox` attribute applies restrictions to content loaded inside an iframe.

Example:

    <iframe
        id="victim_website"
        src="https://victim-website.example"
        sandbox="allow-forms">
    </iframe>

The important point is that sandbox restrictions can prevent certain actions by the framed page.

---

## `allow-forms`

The `allow-forms` permission allows forms inside the sandboxed iframe to continue functioning.

Example:

    sandbox="allow-forms"

This is useful when the target application relies on form submissions.

At the same time, the sandbox can prevent the framed page from performing certain top-level navigation actions that a frame-busting script may attempt.

### Important distinction

The sandbox does **not** hide the iframe or the decoy.

Instead:

    sandbox
       ↓
    Restricts what the framed page can do

while:

    CSS + opacity + z-index
       ↓
    Controls the clickjacking visual overlay

These are separate concepts.

---

## Bypassing Frame Busting

The general attack idea is:

    Target page
         ↓
    Frame-busting JavaScript
         ↓
    iframe sandbox restrictions
         ↓
    Frame buster cannot escape normally
         ↓
    Clickjacking can continue

The attacker can therefore combine:

- A transparent iframe
- A decoy element
- CSS positioning
- `sandbox`
- `allow-forms`

---

# Lab Methodology

## 1. Identify the target page

Log in to the target application and navigate to the account functionality that contains the sensitive action.

---

## 2. Confirm the framing protection

The application uses frame-busting behavior to attempt to prevent the page from being embedded inside an iframe.

Normally this would interfere with the clickjacking attack.

---

## 3. Add the iframe sandbox

The iframe can be created with:

    <iframe
        sandbox="allow-forms"
        src="https://TARGET-LAB-ID.web-security-academy.net/my-account">
    </iframe>

The sandbox restricts the framed page's ability to perform top-level navigation while still allowing the required form behavior.

---

## 4. Create the decoy

Example:

    <div>Test me</div>

Position the decoy over the target action.

Initially use:

    opacity: 0.1;

This makes alignment easier.

---

## 5. Align the Target

Adjust:

- `top`
- `left`
- `width`
- `height`

until the decoy is positioned over the target action.

Use the hand cursor as an indication that the underlying target element is correctly aligned.

---

## 6. Make the iframe Transparent

After alignment:

    opacity: 0.0001;

The target interface becomes almost invisible.

---

## 7. Deliver the Exploit

Change:

    Test me

to:

    Click me

Store the exploit and use the simulated victim functionality to deliver the attack.

---

# Key Concept

Frame busting and iframe sandboxing operate at different levels.

    Frame Busting
        ↓
    Client-side JavaScript
        ↓
    Attempts to escape the iframe

    iframe sandbox
        ↓
    Browser-enforced restrictions
        ↓
    Limits what the framed page can do

The goal of this technique is to restrict the frame-busting behavior while keeping the target functionality required for the clickjacking attack.

---

## Key Takeaways

- Frame busting is a client-side clickjacking defense.
- Frame-busting scripts commonly try to detect iframe execution and escape the frame.
- Frame busting can sometimes be bypassed.
- The HTML5 `sandbox` attribute can restrict behavior inside an iframe.
- `sandbox="allow-forms"` allows forms while applying other sandbox restrictions.
- Sandbox restrictions and visual clickjacking techniques solve different problems.
- `opacity`, `z-index`, and positioning are still responsible for creating the deceptive interface.
- Server-side protections such as `X-Frame-Options` and CSP `frame-ancestors` are stronger mechanisms for controlling framing.

## Lab Status

Completed