# Clickjacking with Prefilled Form Input

## Overview

Some web applications populate form fields using values supplied through URL parameters.

This behavior can be combined with clickjacking so that attacker-controlled data is already present in the target form when the victim interacts with it.

The attacker does not need to manually type the value.

---

## How It Works

Consider an account page that accepts an `email` parameter:

    /my-account?email=hacker@attacker.example

The application may use the parameter to populate the email field:

    <input
        type="email"
        name="email"
        value="hacker@attacker.example"
    >

The attacker can then load this URL inside the clickjacking iframe.

The victim sees the decoy page, while the real form underneath already contains the attacker-controlled value.

---

## Attack Flow

    Attacker-controlled URL parameter
                ↓
       Target form is prefilled
                ↓
       Target page inside iframe
                ↓
          Visible decoy
                ↓
          Victim clicks
                ↓
       Form is submitted
                ↓
     Prefilled value is processed

---

## Finding the Parameter

A useful testing methodology is to inspect the application's behavior when modifying URL parameters.

For example:

    /my-account?email=test@test.com

If the response contains:

    <input type="email" name="email" value="test@test.com">

the parameter is influencing the form.

This indicates that the URL parameter can potentially be used in a clickjacking attack.

---

## Building the Clickjacking Page

The target URL is placed inside the iframe:

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker.example">
    </iframe>

The decoy is then positioned over the target action.

Example:

    <div>Click me</div>

The victim believes they are interacting with the visible decoy, while the actual target form is underneath it.

---

## Practical Methodology

### 1. Identify the target form

Find the sensitive form or action on the target account page.

### 2. Test URL parameters

Try parameters that may influence form fields.

Example:

    ?email=test@test.com

### 3. Confirm the value is prefilled

Inspect the HTML and verify that the supplied value appears inside the form.

### 4. Place the URL inside the iframe

Example:

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker.example">
    </iframe>

### 5. Align the decoy

Initially use:

    opacity: 0.1;

Adjust the position of the decoy until it is aligned with the target action.

### 6. Make the iframe transparent

After alignment:

    opacity: 0.0001;

### 7. Deliver the exploit

Change the visible decoy text to:

    Click me

Store the exploit and deliver it to the simulated victim.

---

## Important Concept

The attacker is combining **two behaviors**:

    URL parameter
          ↓
    Prefilled form value

and:

    Transparent iframe
          ↓
       Clickjacking

Together:

    Attacker-controlled parameter
              ↓
       Prefilled target form
              ↓
       Clickjacking iframe
              ↓
          Victim click
              ↓
       Target action executes

---

## Pentester Mindset

When testing a web application, do not only look for parameters that directly execute code.

Also ask:

- Does a URL parameter modify a form field?
- Can attacker-controlled data be prefilled?
- Is the form submitted through a sensitive action?
- Can the page be framed?
- Can a decoy be positioned over the submission button?

A parameter that appears harmless by itself may become security-relevant when combined with another vulnerability or browser behavior.

---

## Key Takeaways

- URL parameters can be used to prefill form fields.
- Test whether attacker-controlled values are reflected into form inputs.
- Prefilled values can be combined with clickjacking.
- The attacker can control the value before the victim interacts with the page.
- The target page remains legitimate and is loaded inside the iframe.
- Clickjacking provides the deceptive user interaction.
- Always verify the complete attack chain rather than testing each behavior in isolation.

## Lab Status

Completed