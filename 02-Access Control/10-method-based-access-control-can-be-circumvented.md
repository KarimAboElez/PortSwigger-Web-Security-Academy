# Lab: Method-Based Access Control Can Be Circumvented

## Objective:
Escalate the privileges of the user "wiener" to Administrator by bypassing the application's access control.

--- 

## Steps:

1. I first accessed the administrator panel.

2. While browsing the admin functionality, I intercepted the following request used to upgrade a user's privileges:

GET /admin-roles?username=wiener&action=upgrade

3. I observed that this request was responsible for promoting a user to the Administrator role.

4. I copied the request into Burp Repeater.

5. I logged back into the normal user account (wiener).

6. I replayed the same request from my own session.

7. The server processed the request successfully and upgraded my account to Administrator, even though I was not authorized to perform this action.

8. After my privileges were elevated, I accessed:

/admin

9. The administrator panel was now accessible from my account.

10. I deleted the user Carlos.

The lab was successfully solved.

--- 

## Vulnerability:
Method-Based Access Control Bypass (Vertical Privilege Escalation)

## Cause:
The application failed to verify whether the authenticated user was authorized to execute administrative actions. It only checked whether the request reached the correct endpoint, allowing a normal user to replay an administrator's request and perform privileged operations.

## Takeaway:
Never trust that a request originates from the administrator interface.

Every sensitive action must be protected by server-side authorization checks.

When testing Access Control, always inspect administrator requests such as:

- Promote User
- Delete User
- Change Roles
- Reset Password
- Create User
- Disable User

If replaying these requests from a low-privileged account succeeds, the application is vulnerable to Vertical Privilege Escalation.

Always verify that the server enforces authorization independently of the client or the user interface.
