# Lab: Referer-Based Access Control

## Objective:
Exploit the application's flawed Referer-based access control to promote the user "wiener" to Administrator.

--- 

## Steps:

1. I logged into the administrator account using the provided credentials:

Username: administrator
Password: admin

2. I accessed the administrator panel and upgraded a user's role while intercepting the request.

3. I captured the following request:

GET /admin-roles?username=wiener&action=upgrade

4. I noticed that the application relied on the Referer header for authorization.

The request contained:

Referer: https://<LAB-ID>.web-security-academy.net/admin

5. I logged out of the administrator account and logged back in as:

Username: wiener
Password: peter

6. I replaced the administrator's session cookie with my own (wiener) session cookie.

7. I replayed the exact same request while keeping the Referer header pointing to:

Referer: https://<LAB-ID>.web-security-academy.net/admin

8. Although I was authenticated as a normal user, the server accepted the request because it trusted the Referer header instead of performing proper authorization checks.

9. My account (wiener) was successfully promoted to Administrator.

10. The lab was successfully solved.

--- 

## Vulnerability:
Referer-Based Access Control Bypass

## Cause:
The application relied on the client-controlled Referer header to determine whether the request originated from the administrator panel. Since HTTP headers can be modified by an attacker, this check provided no real security. By replaying the request with a forged Referer header, a normal user was able to perform an administrative action.

## Takeaway:
The Referer header should never be used as an authorization mechanism.

Because HTTP headers are fully controlled by the client, attackers can easily forge values such as:

- Referer
- Origin
- User-Agent
- X-Forwarded-For
- X-Original-URL

Authorization decisions must always be enforced on the server side using the authenticated user's permissions, not by trusting client-supplied headers.
