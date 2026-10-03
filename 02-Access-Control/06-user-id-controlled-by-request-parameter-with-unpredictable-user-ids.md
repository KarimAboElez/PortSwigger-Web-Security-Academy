# Lab: User ID Controlled by Request Parameter, with Unpredictable User IDs

## Objective:
Exploit a Horizontal Privilege Escalation vulnerability to obtain Carlos's API key using his GUID.

---

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. Unlike the previous lab, I noticed that users were identified using GUIDs instead of usernames.

3. Since the GUID could not be guessed, I searched the application for Carlos's profile.

4. I opened one of Carlos's blog posts and clicked on his username.

5. The application disclosed Carlos's GUID in the URL.

6. I copied Carlos's GUID.

7. I modified the account request from my own GUID to Carlos's GUID:

GET /my-account?id=<Carlos_GUID>

8. I forwarded the request.

9. The application displayed Carlos's account page and revealed his API key.

10. I copied the API key and submitted it as the solution.

The lab was successfully solved.

---

## Vulnerability:
User ID Controlled by Request Parameter with Unpredictable User IDs (Horizontal Privilege Escalation / IDOR)

## Cause:
The application attempted to prevent IDOR attacks by replacing predictable usernames with GUIDs. However, the GUIDs were publicly exposed through other parts of the application, such as blog author profiles. Since the server still failed to verify whether the authenticated user was authorized to access the requested account, an attacker could obtain another user's GUID and access their sensitive information.

## Takeaway:
Using GUIDs instead of sequential or predictable identifiers does not prevent IDOR vulnerabilities.

Always verify authorization on the server side, even when using random identifiers.

When testing applications, look for places where object identifiers are exposed, including:

- Blog author profiles
- User profiles
- Public comments
- API responses
- HTML source
- JavaScript files

If a valid identifier can be discovered and used to access another user's resources without proper authorization, the application is vulnerable to Horizontal Privilege Escalation (IDOR).
