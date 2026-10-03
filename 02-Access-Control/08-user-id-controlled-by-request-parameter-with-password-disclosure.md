# Lab: User ID Controlled by Request Parameter with Password Disclosure

## Objective:
Retrieve the administrator's password and use it to delete the user "Carlos".

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

id=administrator

5. I forwarded the modified request.

6. The application displayed the administrator's account page.

7. Although the password field appeared masked in the browser, the actual password was present in the HTML source inside the value attribute.

8. I inspected the response and found the administrator's password:

fql9wkgxnme9hyt9mlbh

9. I logged in as the administrator using the disclosed password.

10. I accessed:

/admin

11. I clicked:

Delete user Carlos

12. Carlos was successfully deleted, and the lab was solved.

--- 

## Vulnerability:
User ID Controlled by Request Parameter with Password Disclosure

## Cause:
The application was vulnerable to an IDOR because it trusted the user-controlled "id" parameter without performing proper authorization checks. Additionally, it exposed the user's current password inside the HTML source by pre-filling the password field, allowing an attacker to retrieve another user's password simply by requesting their profile.

## Takeaway:
Never pre-fill password fields with the user's current password.

Password fields should always be empty, and passwords must never be sent back to the client under any circumstances.

When testing IDOR vulnerabilities, always inspect the response source carefully for sensitive information such as:

- Passwords
- API Keys
- Access Tokens
- Secret Keys
- Personal Information

Even if the data is hidden or masked in the browser, it may still be present in the HTML source or response body.
