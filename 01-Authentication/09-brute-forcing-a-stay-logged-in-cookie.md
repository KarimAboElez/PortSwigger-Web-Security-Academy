# Lab: Brute-forcing a Stay-Logged-In Cookie

## Objective

Brute-force Carlos's persistent login cookie in order to access his account without knowing his password.

---

## Vulnerability

The application uses a weak "Stay logged in" cookie.

The cookie is constructed as:

```
Base64(username:MD5(password))
```

Since the password is hashed using **MD5** without any salt or secret, an attacker can generate valid cookies by hashing candidate passwords offline.

---

## Steps

### Phase 1 - Analyze Your Own Cookie

Login using:

```
Username: wiener
Password: peter
```

Enable:

```
Stay logged in
```

After logging in, inspect the cookie:

```
stay-logged-in=
```

Decode it using Burp Decoder.

Example:

```
d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw
```

↓

```
wiener:51dc30ddc473d43a6011e9ebba6ca770
```

Notice that the second value is an **MD5 hash**.

Verify it by hashing your password:

```
MD5(peter)

↓

51dc30ddc473d43a6011e9ebba6ca770
```

This confirms that the cookie format is:

```
Base64(username:MD5(password))
```

---

### Phase 2 - Prepare the Attack

Logout.

Capture:

```
GET /my-account
```

Send the request to **Burp Intruder**.

Change:

```
/my-account?id=wiener
```

to

```
/my-account?id=carlos
```

Highlight only the value of:

```
stay-logged-in=
```

---

### Phase 3 - Configure Intruder

Attack Type:

```
Sniper
```

Payload:

Load the **Candidate Passwords** wordlist.

---

### Phase 4 - Payload Processing

Configure the following rules **in order**:

```
Hash
```

Algorithm:

```
MD5
```

↓

```
Add Prefix

carlos:
```

↓

```
Encode

Base64
```

The generated payload becomes:

```
password

↓

MD5(password)

↓

carlos:MD5(password)

↓

Base64(carlos:MD5(password))
```

---

### Phase 5 - Detect the Correct Cookie

Configure **Grep - Match**.

Search for:

```
Update email
```

Start the attack.

Only one response should contain:

```
Update email
```

This indicates that the generated cookie is valid.

---

### Phase 6 - Access Carlos's Account

The successful request authenticates as Carlos.

Lab Solved ✅

---

## Why does this vulnerability exist?

The application stores:

```
username
+
MD5(password)
```

inside a client-controlled cookie.

Since MD5 is deterministic and unsalted, attackers can compute valid cookies offline using a password wordlist.

The server trusts this cookie as proof of authentication.

---

## Prevention

- Never store password hashes inside authentication cookies.
- Use randomly generated session identifiers.
- Sign persistent cookies using HMAC.
- Use secure server-side sessions.
- Never rely on reversible or predictable client-side authentication tokens.

---

## Tools Used

- Burp Suite
    - Proxy
    - Decoder
    - Intruder

---

## Skills Learned

- Cookie Analysis
- Base64 Encoding / Decoding
- MD5 Hashing
- Offline Password Guessing
- Payload Processing
- Authentication Bypass

---

## Real World Scenario

Some legacy applications store:

```
username:password_hash
```

inside persistent cookies.

If the hash algorithm is weak and predictable, attackers can generate valid authentication cookies without interacting with the login page.

---

## Key Takeaway

**Authentication cookies should never contain predictable values such as password hashes. Persistent login tokens must be random, signed, and validated server-side.**
