# Lab: User ID Controlled by Request Parameter

## Objective:
Exploit a Horizontal Privilege Escalation vulnerability to obtain Carlos's API key.

---

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I opened my account page and intercepted the following request:

GET /my-account?id=wiener

3. I noticed that the application uses the "id" request parameter to determine which user's profile should be displayed.

4. I modified the request parameter from:

id=wiener

to

id=carlos

5. I forwarded the modified request.

6. The application returned Carlos's account page without performing any authorization checks.

7. I located Carlos's API key:

355LLQQdDMPIkqoZGtiPl5P6H6hkkoek

8. I submitted the API key, and the lab was solved.

---

## Vulnerability:
User ID Controlled by Request Parameter (Horizontal Privilege Escalation / IDOR)

## Cause:
The application trusted the user-controlled "id" parameter to identify which account should be displayed. It failed to verify whether the authenticated user was authorized to access the requested profile, allowing any logged-in user to view another user's sensitive information.

## Takeaway:
Whenever you encounter parameters such as:

- id
- user
- username
- account
- profile
- uid
- userid
- customerId

Always try replacing the current user's identifier with another valid user's identifier.

If the application returns another user's information without proper authorization checks, it is vulnerable to Horizontal Privilege Escalation (IDOR).

Authorization must always be enforced on the server side and should never rely solely on client-supplied identifiers.
