# Lab: Unprotected Admin Functionality

## Objective:
Delete the user "Carlos" by accessing an unprotected administrator panel.

---

## Steps:

1. The first thing I checked was:

GET /robots.txt

2. The robots.txt file contained the following entry:

Disallow: /administrator-panel

3. I navigated to:

/administrator-panel

4. The administrator panel was accessible without any authentication or authorization checks.

5. I clicked on:

Delete user Carlos

6. Carlos was successfully deleted, and the lab was solved.

---

## Vulnerability

Unprotected Admin Functionality

---

## Root Cause

The application attempted to hide the administrator panel by listing it inside the robots.txt file instead of protecting it with proper authorization controls.

As a result, any user who discovered the URL could access the administrator panel and perform administrative actions.

---

## Impact

An attacker can gain unauthorized access to administrative functionality and perform privileged actions such as deleting users, modifying application settings, or compromising the entire application.

---
## Key Takeaway

The robots.txt file is not a security mechanism. It only instructs search engines which paths should not be indexed.

Sensitive functionality must always be protected with proper authentication and authorization, regardless of whether the endpoint is publicly known.

---

## Bug Bounty Tip

Always check:

- /robots.txt
- /sitemap.xml

These files may disclose:

- Hidden admin panels
- Backup files
- Debug pages
- Internal endpoints

If any disclosed endpoint lacks proper authorization, it may lead to an Access Control vulnerability.
