# Lab: Password reset broken logic

## Objective

Exploit the flawed password reset logic to reset Carlos's password and access his account.

---

## Vulnerability

The application validates the password reset token but does not verify that the token belongs to the specified username.

As a result, a valid reset token generated for one user can be used to reset another user's password.

---

## Lab Credentials

```
Your Account
Username: wiener
Password: peter

Victim
Username: carlos
```

---

## Steps

1. Login using your own account.

```
Username: wiener
Password: peter
```

2. Navigate to **Forgot Password**.

3. Request a password reset for your own account.

```
Username: wiener
```

4. Capture the password reset request in Burp Suite.

Example:

```
POST /forgot-password?temp-forgot-password-token=<TOKEN>
```

5. Observe the request body.

```
temp-forgot-password-token=<TOKEN>
username=wiener
new-password-1=123
new-password-2=123
```

6. Replace the username with the victim's username.

```
username=carlos
```

7. Keep the same valid reset token.

8. Send the modified request.

9. Carlos's password is successfully changed.

10. Login using:

```
Username: carlos
Password: 123
```

11. Access Carlos's **My Account** page.

---

## Result

Successfully reset Carlos's password using a password reset token generated for another user.

Lab Solved ✅

---

## Why does this vulnerability exist?

The server validates that the password reset token is valid.

However, it **fails to verify that the token belongs to the same user whose password is being changed.**

Instead of binding the reset token to a specific account, the application blindly trusts the `username` parameter supplied by the client.

---

## Prevention

- Bind every password reset token to a single user account.
- Never trust the `username` parameter supplied by the client.
- Retrieve the associated user directly from the reset token on the server.
- Invalidate reset tokens immediately after use.
- Use short-lived, cryptographically secure reset tokens.

---

## HTTP Flow

```
Forgot Password (wiener)
        │
        ▼
Generate Reset Token
        │
        ▼
Attacker Captures Token
        │
        ▼
Modify

username = carlos

        │
        ▼
Server accepts request ❌
        │
        ▼
Carlos Password Changed
```

---

## Tools Used

- Burp Suite
  - Proxy
  - Repeater

---

## Skills Learned

- Password Reset Logic
- Business Logic Vulnerability
- Parameter Manipulation
- Token Validation
- Authentication Logic

---

## Real World Scenario

Many vulnerable applications validate only whether the reset token is valid, but fail to verify that it belongs to the account whose password is being reset.

If the username or user ID is taken directly from client-controlled input, an attacker can reuse their own valid reset token to change another user's password.

---

## Key Takeaway

**A password reset token must always be bound to a specific user account. Never trust user identifiers supplied by the client during the password reset process.**
