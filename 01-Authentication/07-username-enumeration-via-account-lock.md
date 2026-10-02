# Lab: Username enumeration via account lock

## Objective

Enumerate a valid username by abusing the account lock mechanism, then brute-force the user's password and access the account.

---

## Vulnerability

The application locks existing accounts after several failed login attempts.

Non-existent accounts are never locked.

This difference allows attackers to determine whether a username exists.

---

## Steps

### Phase 1 - Discover the Lock Threshold

1. Attempt several failed logins using a random username.

Example:

```
Username: test
Password: test123
```

No account lock occurs because the username does not exist.

This indicates that only valid accounts can be locked.

---

### Phase 2 - Enumerate the Username

Capture the following request:

```http
POST /login
```

Send it to **Intruder**.

Attack Type:

```
Cluster Bomb
```

Use **2 Payload Positions**.

#### Payload 1

Load the **Candidate Usernames** wordlist.

#### Payload 2

Instead of using a password list, use a simple payload that repeats the same password several times.

Example:

```
test
```

Generate **5 payloads** (or enough to trigger the account lock).

When the response changes to:

```
Account locked
```

the username is confirmed to be valid.

```
Valid Username: ar
```

---

### Phase 3 - Brute Force the Password

Switch the attack type to:

```
Sniper
```

Keep the username fixed.

```
Username: ar
```

Load the **Candidate Passwords** wordlist.

Eventually the correct password is found.

```
Password: jennifer
```

---

### Phase 4 - Login

Login successfully using:

```
Username: ar
Password: jennifer
```

Lab Solved ✅

---

## Why does this vulnerability exist?

The application behaves differently depending on whether the username exists.

Existing accounts become locked after several failed attempts.

Non-existent accounts never reach the lock state.

Attackers can exploit this behavior to enumerate valid usernames before launching password attacks.

---

## Prevention

- Return identical responses for valid and invalid usernames.
- Apply the same lock behavior regardless of whether the username exists.
- Avoid revealing account status.
- Implement rate limiting and MFA.

---

## Tools Used

- Burp Suite
  - Proxy
  - Intruder

---

## Skills Learned

- Username Enumeration
- Account Lock Logic Flaw
- Cluster Bomb
- Sniper

---

## Real World Scenario

Many applications lock valid accounts after multiple failed login attempts.

If invalid usernames never trigger the same behavior, attackers can enumerate existing users simply by observing which accounts become locked.

---

## Key Takeaway

**Security features themselves can leak information if they behave differently for valid and invalid users.**
