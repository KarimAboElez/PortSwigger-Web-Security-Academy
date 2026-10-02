# Lab: Username enumeration via response timing

## Objective

Enumerate a valid username using response timing, bypass the IP-based brute-force protection, then brute-force the user's password.

---

## Vulnerability

The application leaks valid usernames through differences in response time.

Additionally, the brute-force protection relies only on the client IP address, which can be bypassed by spoofing the IP using the `X-Forwarded-For` header.

---

## Lab Credentials

```
Your Account
Username: wiener
Password: peter
```

---

## Steps

### Phase 1 - Username Enumeration

1. Login using your own account.

```
Username: wiener
Password: peter
```

2. Capture the `POST /login` request.

3. Replace the password with a very long incorrect password.

Example:

```
peterpeterpeterpeterpeter...
```

4. Send the request to Intruder.

5. Load the **Candidate Usernames** wordlist.

6. Observe the **Response Time (ms)**.

The valid username takes noticeably longer because the application performs password hash verification.

---

### Phase 2 - Bypass IP Rate Limiting

The application allows only **3 login attempts** before blocking the IP for **30 seconds**.

To bypass this protection, add the following header:

```http
chose this in intruder
Sniper attack------->Pitchfork attack
then u can make 2 paylods
X-Forwarded-For: §number§
and the username:----->§wiener§....
```

Use a second payload to increment the value.

Example:

```
1
2
3
4
5
...
```

Each request appears to come from a different IP address.

---

### Phase 3 - Password Brute Force

1. Keep the discovered username fixed.

2. Load the **Candidate Passwords** wordlist.

3. Continue changing the `X-Forwarded-For` value for every request.

4. Identify the successful password.

---

### Phase 4 - Login

Login using the discovered credentials.

If the login request is still rate limited, intercept the request and manually change:

```http
X-Forwarded-For: 777
```

(or any unused value)

The application accepts the request and grants access.

---

## Result

Successfully enumerated the username using response timing.

Bypassed the IP-based brute-force protection.

Brute-forced the victim's password.

Logged into the account.

Lab Solved ✅

---

## Why does this vulnerability exist?

The application compares password hashes only when the username exists.

This causes valid usernames to take slightly longer to process, creating a measurable timing difference.

The brute-force protection is also flawed because it trusts the client-controlled `X-Forwarded-For` header instead of the real client IP.

An attacker can simply change this header to avoid rate limiting.

---

## Prevention

- Ensure identical processing time for valid and invalid usernames.
- Avoid leaking timing differences.
- Never trust client-controlled headers such as:

```
X-Forwarded-For
```

unless they are inserted by a trusted reverse proxy.

- Implement server-side rate limiting based on the real client IP.
- Use account lockout and MFA.

---

## Tools Used

- Burp Suite
  - Proxy
  - Intruder
  - Repeater

---

## Skills Learned

- Timing Attack
- Username Enumeration
- Response Time Analysis
- IP-based Rate Limit Bypass
- Header Manipulation
- Password Brute Force

---

## Real World Scenario

Many web applications attempt to prevent brute-force attacks by limiting login attempts per IP address.

If the application trusts the `X-Forwarded-For` header supplied by the client, attackers can spoof different IP addresses and completely bypass the protection.

Similarly, timing differences during authentication may reveal whether a username exists, even when identical error messages are displayed.

---

## Key Takeaway

**Authentication must be constant in both response content and response time, and security decisions must never rely on client-controlled headers such as `X-Forwarded-For`.**
