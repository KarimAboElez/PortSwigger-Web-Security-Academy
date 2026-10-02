# Lab: Username enumeration via subtly different responses

## Objective

Enumerate a valid username by identifying subtle differences in login responses, then brute-force the user's password.

---

## Vulnerability

The application returns almost identical error messages for valid and invalid usernames.

However, a tiny difference in the response allows attackers to enumerate valid usernames.

---

## Steps

1. Open Burp Suite.

2. Attempt to login using random credentials.

```
Username: test
Password: test123
```

3. Capture the `POST /login` request.

4. Send the request to **Intruder**.

5. Load the **Candidate Usernames** wordlist.

6. Keep the password fixed.

```
Password: buster
```

7. Start the attack.

8. Compare the responses carefully.

Most responses contain:

```
Invalid username or password.
```

Notice the period (`.`) at the end.

One response returns:

```
Invalid username or password
```

without the final period.

That username is the valid account.

```
Valid Username: info
```

9. Replace the username payload with the **Candidate Passwords** wordlist.

10. Keep the username fixed.

```
Username: info
```

11. Start the attack again.

12. The successful password is identified by the redirect response.

```
HTTP/2 302 Found

Location: /my-account?id=info
```

13. Login successfully.

---

## Result

```
Username: info
Password: buster
```

Lab Solved ✅

---

## Why does this vulnerability exist?

Although the application attempts to hide username enumeration by displaying generic error messages, the responses are not completely identical.

A single missing character (the final period) reveals whether the username exists.

This allows attackers to enumerate valid usernames before brute-forcing passwords.

---

## Prevention

- Return identical error messages.
- Ensure responses have identical:
  - Content
  - Length
  - Characters
  - Timing
- Implement Rate Limiting.
- Implement Account Lockout.
- Use Multi-Factor Authentication (MFA).

---

## Tools Used

- Burp Suite
  - Proxy
  - Intruder

---

## Skills Learned

- Username Enumeration
- Response Comparison
- Character-by-Character Analysis
- Password Brute Force

---

## Real World Scenario

Some applications attempt to hide username enumeration by using generic error messages.

However, even a single extra character, missing punctuation mark, or whitespace difference can leak whether a username exists.

Attackers can automate these comparisons to enumerate valid accounts.

---

## Key Takeaway

**Even a one-character difference in an authentication response can leak sensitive information. Every failed login response must be completely identical.**
