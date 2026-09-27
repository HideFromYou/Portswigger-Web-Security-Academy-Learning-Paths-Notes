# Clickjacking with DOM XSS

## Overview

Clickjacking can be combined with a DOM-based XSS vulnerability.

In this scenario, clickjacking is used as the delivery mechanism for an existing client-side XSS vulnerability.

The general approach is:

    Find DOM XSS
          ↓
    Identify the source
          ↓
    Identify the sink
          ↓
    Construct the malicious URL
          ↓
    Load the URL inside an iframe
          ↓
    Trick the victim into triggering the vulnerable behavior

---

## DOM XSS

DOM-based XSS occurs when attacker-controlled data reaches a dangerous client-side DOM sink and is interpreted as executable content.

A useful mental model is:

    Attacker-controlled input
            ↓
          Source
            ↓
       DOM processing
            ↓
           Sink
            ↓
        XSS execution

### Source

The source is where attacker-controlled data enters the JavaScript environment.

Common examples include URL-controlled data such as:

    location.search

or values extracted from URL parameters.

### Sink

The sink is the operation that uses the attacker-controlled data in a dangerous way.

The important testing methodology is therefore:

    Source → Data flow → Sink → Execution

---

# Finding the XSS

Before combining DOM XSS with clickjacking, identify and confirm the XSS vulnerability.

For example, a vulnerable feedback form may accept a `name` parameter.

A test payload can be:

    <img src=1 onerror=print()>

If the payload executes, the DOM XSS behavior has been confirmed.

The exact payload depends on the vulnerable context.

---

## Using a URL Parameter

If the vulnerable application accepts parameters through the URL, the payload can be URL encoded.

Example:

    /feedback?name=%3Cimg%20src%3D1%20onerror%3Dprint()%3E

The encoded payload is decoded by the application and can reach the vulnerable DOM sink.

---

# Combining DOM XSS with Clickjacking

Once the DOM XSS has been identified, construct a URL that triggers the vulnerability.

The URL is then loaded inside the clickjacking iframe.

Example structure:

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/feedback?name=PAYLOAD">
    </iframe>

The attacker then creates a decoy element positioned over the action that causes the vulnerable behavior to execute.

---

## Lab Methodology

### 1. Find the vulnerable input

Identify the feedback form or other input that is processed by client-side JavaScript.

### 2. Confirm DOM XSS

Test the input with a harmless proof-of-concept payload.

Example:

    <img src=1 onerror=print()>

### 3. Identify the source

Determine where the attacker-controlled data originates.

For example:

    URL parameter
          ↓
    location.search
          ↓
    URLSearchParams
          ↓
    parameter value

### 4. Identify the sink

Trace where the value is inserted into the DOM.

The goal is to understand:

    Where does my input enter?
              ↓
    Where does it go?
              ↓
    What operation finally interprets it?

### 5. Build the malicious URL

Construct a URL containing the XSS payload.

Example structure:

    /feedback?name=PAYLOAD

Additional parameters may be required so that the page loads the relevant form state.

### 6. Load the URL inside the iframe

Example:

    <iframe src="https://TARGET-LAB-ID.web-security-academy.net/feedback?name=PAYLOAD">
    </iframe>

### 7. Position the decoy

Use the same clickjacking technique:

    opacity: 0.1;

while aligning the decoy.

After alignment:

    opacity: 0.0001;

### 8. Deliver the exploit

Change the decoy text to encourage the victim to interact with it.

Then store the exploit and deliver it to the simulated victim.

---

# Important Concept

The clickjacking vulnerability and the DOM XSS vulnerability perform different roles.

    DOM XSS
       ↓
    Provides the malicious behavior

    Clickjacking
       ↓
    Tricks the victim into triggering it

Together:

    Malicious URL
          ↓
    DOM XSS inside target page
          ↓
    Target page loaded in iframe
          ↓
    Decoy interface
          ↓
    Victim interaction
          ↓
    XSS executes

---

# Pentester Mindset

When investigating a possible DOM XSS + clickjacking chain, ask:

1. Can I control an input through the URL?
2. Where is that input read by JavaScript?
3. What DOM sink receives the value?
4. Can I construct a URL that triggers the XSS?
5. Can the vulnerable page be loaded inside an iframe?
6. What user interaction triggers the vulnerable behavior?

The key is to understand the complete data flow rather than simply finding an XSS payload.

---

## Key Takeaways

- DOM XSS can be combined with clickjacking.
- Identify and confirm the DOM XSS first.
- Find the attacker-controlled source.
- Trace the data to the dangerous sink.
- Construct a URL that triggers the XSS.
- Load that URL inside the clickjacking iframe.
- Clickjacking can then be used to trick the victim into triggering the vulnerable behavior.
- The core DOM XSS methodology is:

    Source → Data flow → Sink → Execution

## Lab Status

Completed