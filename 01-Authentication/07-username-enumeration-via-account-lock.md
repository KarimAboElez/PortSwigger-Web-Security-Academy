
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
