# Lab: 2FA simple bypass

## Objective

Bypass the two-factor authentication mechanism and access Carlos's account without knowing the verification code.

---

## Vulnerability

The application validates the username and password correctly, but fails to verify whether the user has successfully completed the second authentication step before allowing access to protected resources.

---

## Lab Credentials

```
Your Account
Username: wiener
Password: peter

Victim Account
Username: carlos
Password: montoya
```

---

## Steps

1. Login using Carlos's credentials.

```
Username: carlos
Password: montoya
```

2. After a successful login, the application redirects to:

```
/login2
```

3. Do **not** submit the 2FA verification code.

4. Manually request the account page.

```
GET /my-account
```

or

```
GET /my-account?id=carlos
```

5. The application grants access to the account without verifying the second authentication step.

---

## Result

Successfully accessed Carlos's account without entering the 2FA verification code.

Lab Solved ✅

---

## Why does this vulnerability exist?

After validating the username and password, the application creates an authenticated session immediately.

Instead of marking the session as **Partially Authenticated**, the application allows access to protected pages before verifying the second authentication factor.

As a result, an attacker who already knows the username and password can completely bypass the 2FA process.

---

## Prevention

- Create a **Partially Authenticated Session** after the first login step.
- Require successful completion of the second authentication factor before granting access to protected resources.
- Validate the user's 2FA status on every request to sensitive endpoints.
- Deny access to pages such as `/my-account` until the authentication flow is fully completed.
- Expire partially authenticated sessions after a short period.

---

## HTTP Flow

```
POST /login
        │
        ▼
Username & Password Valid
        │
        ▼
Redirect → /login2
        │
        │  (2FA should be required here)
        ▼
GET /my-account   ← Bypass
        │
        ▼
Access Granted ❌
```

---

## Tools Used

- Burp Suite
  - Proxy
  - Repeater
- Web Browser

---

## Skills Learned

- Two-Factor Authentication (2FA)
- Authentication Bypass
- Authentication State
- Session Validation
- Access Control

---

## Real World Scenario

Many applications implement 2FA as an additional page after the login form.

If the server authenticates the session immediately after verifying the username and password, but forgets to verify that the user completed the second factor before accessing protected resources, attackers can bypass the entire 2FA mechanism simply by requesting protected endpoints directly.

---

## Key Takeaway

**2FA is not secure unless every protected endpoint verifies that the user has successfully completed the second authentication step.**
