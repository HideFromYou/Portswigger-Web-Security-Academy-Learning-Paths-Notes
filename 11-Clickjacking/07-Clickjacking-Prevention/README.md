# Clickjacking Prevention

## Overview

Clickjacking is a browser-side behavior, but effective protection can be provided by server-driven mechanisms that control whether a page is allowed to be loaded inside an iframe.

The two main mechanisms covered are:

- `X-Frame-Options`
- Content Security Policy (CSP)

These mechanisms are communicated to the browser through HTTP response headers.

---

# X-Frame-Options

`X-Frame-Options` is an HTTP response header that allows a website to control whether its pages can be loaded inside frames or iframes.

Example:

    X-Frame-Options: DENY

The browser uses this instruction when deciding whether the page can be framed.

---

## DENY

    X-Frame-Options: DENY

Prevents the page from being framed.

Conceptually:

    Attacker website
          ↓
       <iframe>
          ↓
    Target page
          ✕
      Not allowed

This prevents the basic iframe-based clickjacking technique.

---

## SAMEORIGIN

    X-Frame-Options: SAMEORIGIN

Allows the page to be framed only by pages from the same origin.

Conceptually:

    Same origin
         ↓
      iframe
         ↓
    Target page
         ✓

    Different origin
         ↓
      iframe
         ↓
    Target page
         ✕