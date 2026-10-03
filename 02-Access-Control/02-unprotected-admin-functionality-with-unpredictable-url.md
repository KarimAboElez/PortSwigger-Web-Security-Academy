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
