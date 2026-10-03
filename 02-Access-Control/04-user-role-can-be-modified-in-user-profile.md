# Lab: User Role Can Be Modified in User Profile

## Objective:
Modify the user role to gain administrator privileges and delete the user "Carlos".

---
## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I changed my email address from the "My Account" page.

3. While intercepting the request with Burp Suite, I captured the following request:

POST /my-account/change-email

4. The original request only contained the email field:

{
    "email":"test@gmail.com"
}

5. Since the lab description mentioned that the application uses a roleid of 2 for administrators, I modified the JSON request by adding additional parameters, including the roleid.

The modified request became:

{
    "username":"wiener",
    "email":"test@gmail.com",
    "apikey":"<my_api_key>",
    "roleid":2
}

6. After forwarding the modified request, my account privileges were changed to Administrator.

7. I browsed to:

/admin

8. The administrator panel was now accessible.

9. I clicked:

Delete user Carlos

10. Carlos was successfully deleted, and the lab was solved.

---

## Vulnerability:
User Role Can Be Modified in User Profile

## Cause:
The application accepted sensitive parameters supplied by the client, including the user's role. Instead of enforcing authorization on the server, it trusted user-controlled data, allowing privilege escalation by modifying the roleid value.

## Takeaway:
Never trust client-supplied parameters for authorization.

Whenever a profile update request exists, test whether hidden or undocumented fields can be added, such as:

- role
- roleid
- isAdmin
- admin
- permissions
- userType

If the server accepts these values without proper validation, it may lead to Vertical Privilege Escalation.

Always inspect profile update requests carefully, as they are common targets for Mass Assignment and Privilege Escalation vulnerabilities.
