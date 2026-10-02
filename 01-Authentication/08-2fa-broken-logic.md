# Lab: 2FA Broken Logic

## Objective

Exploit a logic flaw in the two-factor authentication process to access Carlos's account without knowing his credentials.

---

## Vulnerability

The application identifies the user being verified using the client-controlled `verify` cookie.

The OTP verification process is not properly bound to the authenticated session, allowing an attacker to brute-force another user's OTP.

---

## Lab Credentials

```
Username: wiener
Password: peter
```

Victim:

```
Username: carlos
```

---

## Steps

### Phase 1 - Login

Login using your own account.

```
Username: wiener
Password: peter
```

After logging in, the application redirects to:

```
GET /login2
مره في ال POST و مره في ال GET 
هغير ال اسم في الاتنين و همسح السشم بتاعت الكوكي و ابعت قبل ما ابعت علي ال Intruder
```

Retrieve your own OTP from the email client.

---

### Phase 2 - Manipulate the Verification Cookie

Capture the request.

Replace:

```
verify=wiener
```

with

```
verify=carlos
```

This causes the application to start the OTP verification process for Carlos instead of Wiener.

---

### Phase 3 - Brute Force the OTP

Capture the following request:

```http
POST /login2
```

Send it to **Intruder**.

Attack Type:

```
Sniper
```

Select only the OTP value:

```
mfa-code=§0000§
```

Configure the payload:

- Brute Forcer
- 10000
- Minimum length: 4
- Maximum length: 4

This generates all possible OTP values.

```
0000
0001
0002
...
9999
```

Start the attack.

Eventually a successful response returns a valid authenticated session.

---

### Phase 4 - Reuse the Session

Copy the authenticated session cookie returned by the successful request.

Open your browser.

Replace your current session cookie with the newly obtained one.

Refresh the page.

You are now logged in as Carlos.

Lab Solved ✅

---

## Why does this vulnerability exist?

The application trusts the client-controlled `verify` cookie to determine which user is currently performing the second authentication step.

The OTP verification is not securely linked to the authenticated login session.

As a result, attackers can switch the verification target and brute-force another user's OTP.

---

## Prevention

- Bind the OTP challenge to the authenticated server-side session.
- Never trust client-controlled cookies to identify the user being verified.
- Implement rate limiting on OTP verification.
- Expire OTP challenges after several failed attempts.
- Lock verification after repeated failures.

---

## Tools Used

- Burp Suite
  - Proxy
  - Repeater
  - Intruder

---

## Skills Learned

- 2FA Logic Flaws
- Cookie Manipulation
- OTP Brute Force
- Session Hijacking

---

## Real World Scenario

Some applications identify the user undergoing two-factor authentication using client-controlled parameters or cookies.

If the verification challenge is not bound to the authenticated session, attackers may redirect the challenge to another account and brute-force the OTP.

---

## Key Takeaway

**The second authentication step must always be cryptographically and server-side bound to the authenticated login session. Client-controlled identifiers must never determine whose OTP is being verified.**
