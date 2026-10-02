# Lab: Username enumeration via different responses

## Objective
Enumerate a valid username by identifying differences in login responses, then brute-force the user's password to gain access to the account.

---

## Vulnerability

The application returns different responses for valid and invalid usernames.
This allows attackers to enumerate existing usernames before attempting a password brute-force attack.

---

## Steps

1. Open Burp Suite.
2. Attempt to log in with random credentials.

```
Username: test
Password: test123
```

3. Capture the `POST /login` request.
4. Right-click the request and select **Send to Intruder**.
5. Load the **Candidate Usernames** wordlist.
6. Keep the password fixed.

```
Password: test123
```

7. Start the attack.
8. Compare the **Response Length** (or the response message).
9. Identify the username with the different response.

```
Valid Username: att
```

10. Replace the usernames payload with the **Candidate Passwords** wordlist.
11. Keep the username fixed.

```
Username: att
```

12. Start the attack again.
13. Compare the responses until a successful login is found.

```
Password: 987654321
```

14. Log in using the discovered credentials.

---

## Result

```
Username: att
Password: 987654321
```

Lab Solved ✅

---

## Why does this vulnerability exist?

The application leaks information by returning different responses for:

- Invalid username.
- Valid username with an incorrect password.

This allows an attacker to identify valid usernames before performing a password brute-force attack, making the attack much more efficient.

---

## Prevention

- Return a generic error message such as:

```
Invalid username or password.
```

- Ensure all login responses have the same:
  - Status Code
  - Response Length
  - Response Time

- Implement Rate Limiting.
- Implement Account Lockout after multiple failed attempts.
- Use Multi-Factor Authentication (MFA).
- Log and monitor suspicious login attempts.

---

## Tools Used

- Burp Suite
  - Proxy
  - Intruder

---

## Skills Learned

- Username Enumeration
- Response Length Analysis
- Password Brute Force
- Burp Intruder
