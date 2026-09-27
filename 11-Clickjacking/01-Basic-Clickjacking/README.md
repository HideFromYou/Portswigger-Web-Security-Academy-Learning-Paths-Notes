# Basic Clickjacking

## What is Clickjacking?

Clickjacking, also known as UI redressing, is an interface-based attack where an attacker tricks a victim into clicking on an actionable element of a target website while believing they are interacting with a different, decoy interface.

A common technique is to load the target website inside a transparent or nearly transparent `<iframe>` and position a visible decoy element over it.

The victim sees the decoy, but the actual click is received by the target website.

---

## How Clickjacking Works

The attack generally consists of two layers:

    [ Target website inside iframe ]  ← top layer
    [ Visible decoy content ]        ← visual layer

The target iframe is positioned above the decoy using CSS.

The iframe is then made almost invisible using the `opacity` property.

Example:

    <style>
    iframe {
        position: absolute;
        width: 500px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }

    #decoy {
        position: absolute;
        top: 400px;
        left: 80px;
        z-index: 1;
    }
    </style>

    <div id="decoy">Click me</div>

    <iframe src="https://victim-website.example/my-account"></iframe>

The victim sees:

    Click me

But the actual clickable element underneath the cursor belongs to the target website.

---

## Important CSS Properties

### `position`

Controls where the iframe and decoy elements are positioned.

Example:

    position: absolute;

### `z-index`

Controls which element is displayed on top of another element.

Example:

    iframe {
        z-index: 2;
    }

    #decoy {
        z-index: 1;
    }

The iframe is therefore above the decoy.

### `opacity`

Controls transparency.

During testing, an opacity such as:

    opacity: 0.1;

can be useful because the target page remains partially visible.

For the final exploit:

    opacity: 0.0001;

makes the iframe almost completely transparent while still allowing interaction.

---

## Clickjacking vs CSRF

Clickjacking and CSRF are related but different attacks.

### Clickjacking

The victim is tricked into performing an action by interacting with a deceptive interface.

    Victim sees decoy
           ↓
    Victim clicks
           ↓
    Target action executes

### CSRF

The attacker causes the victim's browser to send an unwanted request without requiring the victim to intentionally click the target action.

The important difference is that clickjacking relies on **user interaction**, while CSRF focuses on **forging a request using the victim's authenticated session**.

---

## CSRF Tokens Do Not Automatically Prevent Clickjacking

A CSRF token is designed to prevent unauthorized requests that do not contain a valid token.

However, in a clickjacking attack the legitimate target page can be loaded inside the iframe.

The page may already contain a valid CSRF token in its form.

Example:

    <input type="hidden" name="csrf" value="VALID-CSRF-TOKEN">

When the victim clicks the legitimate button inside the framed page, the legitimate form submission includes the valid token.

Therefore:

    Valid target page
          ↓
    Valid CSRF token
          ↓
    Victim clicks target action
          ↓
    Action executes

A CSRF token by itself is therefore not a clickjacking defense.

---

# Basic Clickjacking Lab

## Objective

The basic PortSwigger lab demonstrates clickjacking against an account page that contains a sensitive action protected by a CSRF token.

The objective is to construct a malicious page that tricks the victim into clicking the target action through a decoy element.

Credentials provided by the lab:

    Username: wiener
    Password: peter

---

## Methodology

### 1. Log in to the target application

Authenticate to the lab using the supplied credentials.

Navigate to the account page and identify the sensitive action that the attacker wants the victim to trigger.

---

### 2. Load the target page inside an iframe

The target account page is placed inside an iframe on the exploit server.

Example:

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/my-account"></iframe>

---

### 3. Create the decoy

Create visible content that encourages the victim to click.

Example:

    <div id="decoy">Test me</div>

The decoy must be positioned so that it visually overlaps the real target button.

---

### 4. Align the elements

Initially use a visible opacity:

    opacity: 0.1;

This makes it possible to see the target interface and correctly position the decoy.

Adjust:

- `top`
- `left`
- `width`
- `height`

until the decoy is aligned with the target action.

The hand cursor is a useful indication that the clickable target is correctly positioned underneath the decoy.

---

### 5. Make the iframe transparent

Once the alignment is correct, change the opacity:

    opacity: 0.0001;

The victim should now see only the decoy interface.

---

### 6. Deliver the exploit

Change the decoy text to something that encourages interaction:

    <div id="decoy">Click me</div>

Store the exploit and use the PortSwigger **Deliver exploit to victim** functionality.

The simulated victim performs the click.

Do not click the final decoy yourself when testing a destructive action, because your own authenticated account may be affected.

---

# Lab Flow

The basic attack can be represented as:

    Attacker-controlled exploit page
                ↓
    Target page loaded in iframe
                ↓
    iframe positioned above decoy
                ↓
    iframe made almost invisible
                ↓
    Victim sees "Click me"
                ↓
    Victim clicks
                ↓
    Target action executes

---

# Pentester Mindset

When testing for clickjacking, ask:

1. Can the target page be loaded inside an iframe?
2. Is there a sensitive action available to an authenticated user?
3. Can the target action be aligned with attacker-controlled content?
4. Does the application rely only on CSRF protection?
5. Is there a server-side framing protection mechanism?
6. Can the victim be convinced to interact with the decoy?

The core question is:

    Can I make the victim believe they are clicking one thing
    while their browser actually clicks something else?

---

# Key Takeaways

- Clickjacking is also known as UI redressing.
- The target website is commonly loaded inside an iframe.
- The iframe can be positioned above a visible decoy.
- `z-index` controls the layer order.
- `opacity` can make the target interface almost invisible.
- Precise positioning is essential.
- Clickjacking normally requires victim interaction.
- CSRF and clickjacking are different attack classes.
- CSRF tokens do not inherently prevent clickjacking.
- During testing, use visible opacity to align the target.
- For the final exploit, the iframe can be made almost transparent.
- For destructive actions, the exploit should be delivered to the simulated victim rather than clicked manually by the tester.

## Lab Status

Completed