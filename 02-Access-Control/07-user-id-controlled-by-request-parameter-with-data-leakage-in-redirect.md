# Lab: User ID Controlled by Request Parameter with Data Leakage in Redirect

## Objective:
Obtain Carlos's API key by exploiting information disclosure in a redirect response.

---

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I intercepted the following request:

GET /my-account?id=wiener

3. I modified the request parameter from:

id=wiener

to

id=carlos

4. After forwarding the request, the server responded with a redirect (302).

5. Although the browser redirected me away from Carlos's profile, I inspected the response body in Burp Suite instead of following the redirect.

6. The response body still contained Carlos's account information, including his API key.

7. I copied Carlos's API key and submitted it as the solution.

The lab was successfully solved.

---

## Vulnerability:
User ID Controlled by Request Parameter with Data Leakage in Redirect

## Cause:
The application attempted to prevent unauthorized access by redirecting the user away from another user's profile. However, the sensitive data was still generated and included in the response body before the redirect occurred. As a result, an attacker could intercept the response and recover confidential information.

## Takeaway:
Do not assume a redirect protects sensitive data.

Whenever you receive a response such as:

- 301 Moved Permanently
- 302 Found
- 303 See Other

Always inspect the response body in Burp Suite.

Sensitive information may still be present even if the browser automatically redirects the user.

This vulnerability combines an IDOR with Information Disclosure caused by improper handling of redirect responses.
