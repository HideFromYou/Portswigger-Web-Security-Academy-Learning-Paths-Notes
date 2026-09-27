# Clickjacking (UI Redressing)

Clickjacking, also known as UI redressing, is a web application attack where a victim is tricked into interacting with a target website through a deceptive interface.

The attacker typically loads the target website inside a transparent or nearly transparent `<iframe>` and places decoy content over or around the target interface.

The victim believes they are interacting with the visible decoy, while their clicks are actually being received by the framed target application.

---

## Learning Path Topics

### 01 - Basic Clickjacking

Introduction to the core clickjacking technique.

Key concepts:

- Transparent or nearly transparent iframes
- Decoy elements
- CSS positioning
- `z-index`
- `opacity`
- Aligning decoy elements with target actions
- Difference between clickjacking and CSRF

---

### 02 - Clickjacking with CSRF Token Protection

A valid CSRF token does not automatically prevent clickjacking.

The target page is legitimate and can contain a valid CSRF token inside the iframe. The victim's interaction with the framed page can therefore still trigger the protected action.

---

### 03 - Clickjacking with Prefilled Form Input

Some applications populate form fields using URL parameters.

Example:

    /my-account?email=hacker@attacker.example

If attacker-controlled data is prefilled into a form, clickjacking can be combined with the form submission to make the victim unknowingly submit attacker-controlled data.

---

### 04 - Frame Busting

Frame-busting scripts are client-side JavaScript techniques designed to prevent a page from being loaded or remaining inside an iframe.

Common techniques include:

- Checking whether the current window is the top-level window
- Preventing framing
- Redirecting the page out of the iframe
- Attempting to prevent interaction with framed content

Frame-busting is client-side and can sometimes be bypassed.

The HTML5 `sandbox` attribute can also be used to restrict the behavior of code inside an iframe.

---

### 05 - Clickjacking with DOM XSS

Clickjacking can be combined with another client-side vulnerability such as DOM-based XSS.

General methodology:

    Attacker-controlled input
            ↓
          Source
            ↓
      DOM processing
            ↓
           Sink
            ↓
       XSS execution

The XSS vulnerability is identified first, then the vulnerable URL/action is loaded inside the clickjacking iframe.

---

### 06 - Multistep Clickjacking

Multistep clickjacking requires the victim to perform multiple actions in sequence.

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

The important concept is that the second decoy must be aligned with the target action on the next page or application state.

---

### 07 - Clickjacking Prevention

Server-driven mechanisms can restrict whether a page is allowed to be loaded inside an iframe.

The two main mechanisms covered are:

- `X-Frame-Options`
- `Content-Security-Policy`

#### X-Frame-Options

Controls whether a page can be framed.

Examples:

    X-Frame-Options: DENY

Prevents framing completely.

    X-Frame-Options: SAMEORIGIN

Allows framing only from the same origin.

#### Content Security Policy

CSP is a broader security mechanism that can provide mitigation against multiple attack classes, including XSS and clickjacking.

For clickjacking, the relevant directive is:

    frame-ancestors

Examples:

    Content-Security-Policy: frame-ancestors 'none';

Prevents any site from framing the page.

    Content-Security-Policy: frame-ancestors 'self';

Allows framing only from the same origin.

    Content-Security-Policy: frame-ancestors normal-website.com;

Restricts framing to a named site.

---

## Clickjacking Mental Model

    Victim sees decoy
           ↓
    Transparent iframe
           ↓
    Real target interface
           ↓
    Victim clicks
           ↓
    Target action is triggered

The attacker controls the visible decoy, while the iframe contains the real application.

---

## Key Takeaways

- Clickjacking is an interface-based attack.
- The target application is commonly loaded inside an iframe.
- The victim sees a decoy but interacts with the underlying target interface.
- CSRF tokens do not inherently prevent clickjacking.
- Frame-busting is a client-side protection mechanism.
- `X-Frame-Options` controls whether framing is allowed.
- CSP `frame-ancestors` controls which origins are allowed to frame a page.
- Clickjacking can be combined with vulnerabilities such as DOM-based XSS.
- Multistep clickjacking can target different actions across multiple application states.
- Defense should use appropriate server-side framing restrictions and a layered security strategy.

---

## Labs Completed

- Basic clickjacking with CSRF token protection
- Clickjacking with prefilled form input
- Clickjacking with frame-busting protection
- Exploiting clickjacking to trigger DOM-based XSS
- Multistep clickjacking

---

## Folder Structure

- [01 - Basic Clickjacking](./01-Basic-Clickjacking/)
- [02 - Clickjacking with CSRF Token](./02-Clickjacking-with-CSRF-Token/)
- [03 - Clickjacking with Prefilled Form Input](./03-Clickjacking-with-Prefilled-Form-Input/)
- [04 - Frame Busting](./04-Frame-Busting/)
- [05 - Clickjacking with DOM XSS](./05-Clickjacking-with-DOM-XSS/)
- [06 - Multistep Clickjacking](./06-Multistep-Clickjacking/)
- [07 - Clickjacking Prevention](./07-Clickjacking-Prevention/)