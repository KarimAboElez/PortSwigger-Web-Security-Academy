# Lab: User Role Controlled by Request Parameter

## Objective:
Access the administrator panel by modifying a forgeable cookie and delete the user "Carlos".

---

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. After logging in, I inspected the browser cookies using the Developer Tools.

3. I found the following cookie:

Admin=false

4. Since the lab description mentioned that the application identifies administrators using a forgeable cookie, I modified the cookie value from:

Admin=false

to

Admin=true

5. After updating the cookie, I refreshed the page and navigated to:

/admin

6. The administrator panel became accessible because the application trusted the client-side cookie to determine user privileges.

7. I clicked on:

Delete user Carlos

8. Carlos was successfully deleted, and the lab was solved.

---
## Vulnerability:
User Role Controlled by Request Parameter

## Cause:
The application stored the user's authorization level inside a client-controlled cookie. Since the cookie was neither protected nor validated on the server, an attacker could simply modify its value and gain administrator privileges.

## Takeaway:
Never trust client-side data for authorization decisions.

Cookies, headers, URL parameters, and hidden form fields can all be modified by an attacker. User roles and permissions must always be verified on the server side.

When testing Access Control vulnerabilities, always inspect:

- Cookies
- Request Parameters
- Hidden Form Fields
- HTTP Headers

Look for values such as:

- admin=false
- role=user
- isAdmin=0
- privileged=no

If these values can be modified to obtain additional privileges, the application is vulnerable to Privilege Escalation.
