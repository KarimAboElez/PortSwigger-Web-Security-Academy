# Lab: Unprotected Admin Functionality with Unpredictable URL

## Objective:
Access the hidden administrator panel and delete the user "Carlos".

---
## Steps:

1. I opened one of the product pages.

2. Since the lab mentioned that the admin panel was located at an unpredictable URL, I inspected the page source and searched through the application's JavaScript files.

3. Inside one of the JavaScript files, I found the following hidden endpoint:

/admin-o2zwlq

4. I copied the endpoint and opened it directly in the browser.

5. The administrator panel was accessible without any authentication or authorization checks.

6. I clicked on:

Delete user Carlos

7. Carlos was successfully deleted, and the lab was solved.

---

## Vulnerability:
Unprotected Admin Functionality with Unpredictable URL

## Cause:
The application attempted to hide the administrator panel by placing it at a random URL instead of protecting it with proper authorization. Although the URL was difficult to guess, it was exposed inside a client-side JavaScript file, allowing any user to discover and access it.

## Takeaway:
When testing a web application, always inspect:

- JavaScript files
- Page Source
- Network requests

These resources often reveal hidden endpoints, API routes, admin panels, or internal functionality. Hiding an endpoint is not a security mechanism. Sensitive functionality must always be protected with proper authentication and authorization.
---
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
