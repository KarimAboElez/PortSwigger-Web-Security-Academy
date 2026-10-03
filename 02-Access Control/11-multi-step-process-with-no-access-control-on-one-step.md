# Lab: Multi-Step Process with No Access Control on One Step

## Objective:
Exploit a flawed multi-step role upgrade process to promote the user "wiener" to Administrator.

--- 

## Steps:

1. I logged into the administrator account using the provided credentials:

Username: administrator
Password: admin

2. I navigated to the administrator panel and started the process of upgrading a user's role.

3. I observed that the role upgrade consisted of multiple steps.

4. After confirming the upgrade action, I intercepted the final request responsible for completing the process:

POST /admin-roles

Request Body:

action=upgrade&confirmed=true&username=wiener

5. I copied this request into Burp Repeater.

6. I logged out of the administrator account and logged back in as:

Username: wiener
Password: peter

7. I replaced the administrator's session cookie with my own session cookie.

8. I replayed the same request:

POST /admin-roles

action=upgrade&confirmed=true&username=wiener

9. The server accepted the request and promoted my account to Administrator, even though I was authenticated as a normal user.

10. My account (wiener) now had administrator privileges, and the lab was successfully solved.

--- 

## Vulnerability:
Multi-Step Process with No Access Control on One Step

## Cause:
The application performed authorization checks during the first step of the role upgrade process, but failed to verify permissions during the final confirmation step. As a result, a low-privileged user could replay the final request and complete the administrative action without proper authorization.

## Takeaway:
Multi-step workflows must enforce authorization checks at every stage, not only at the beginning.

When testing administrative workflows, always inspect every request involved in the process, including:

- Confirmation requests
- Final submission requests
- Approval actions
- Completion endpoints

If one of the later steps lacks authorization checks, it may be possible to bypass the intended security controls and perform privileged actions as a normal user.
