Lab: 2FA Bypass Using a Brute-Force Attack (EXPERT)

Objective:
Exploit a weakness in the Two-Factor Authentication mechanism to brute-force Carlos's OTP code and gain access to his account.

---
Vulnerability

The application protects the login with a 2FA code, but every successful username/password login generates a fresh authenticated session. This makes it possible to brute-force the OTP by automatically logging in before every attempt using Burp Macros.

Victim Credentials

Username: carlos
Password: montoya

---

Step 1 - Capture the Authentication Flow

Log in using Carlos's credentials and capture the following requests:

GET /login

POST /login

GET /login2

These requests represent the complete login flow before the OTP verification page.

---

Step 2 - Create a Burp Macro

Navigate to:

Settings
→ Sessions
→ Macros

Create a new Macro and include the following requests in order:

GET /login

POST /login

GET /login2

Burp automatically extracts the session cookie and any required CSRF token.

Save the Macro.

---
Step 3 - Create a Session Handling Rule

Create a new Session Handling Rule.

Configure it to execute the Macro before every Intruder request.

This causes Burp to:

1. Log in automatically.
2. Receive a fresh authenticated session.
3. Open the OTP page.
4. Send the brute-force request.

---

Step 4 - Configure Intruder

Send the POST /login2 request to Intruder.

Attack Type:

Sniper

Select only the mfa-code parameter as the payload position.

Payload:

0000

↓

9999

---

Step 5 - Start the Attack

Launch the Intruder attack.

Burp executes the Macro before every OTP attempt, creating a new authenticated session each time.

Eventually one request returns:

HTTP/2 302 Found

Location:
/my-account?id=carlos

instead of

HTTP/2 200

This indicates that the correct OTP has been found.

---

Step 6 - Access Carlos's Account

The successful response contains a new authenticated session:

Set-Cookie:
session=xxxxxxxxxxxxxxxx

I simply copied this session value and replaced my current session cookie in the browser using the Inspector.

After refreshing the page, I was logged in as Carlos.

The lab was solved successfully.

---

Tools Used

- Burp Suite Professional
- Proxy
- Intruder
- Burp Macros
- Session Handling Rules

---

Skills Learned

- Burp Macros
- Session Handling Rules
- Automated Authentication
- 2FA Brute Force
- OTP Protection Bypass
- Authentication Workflow Analysis

---

Key Takeaway

Instead of brute-forcing the OTP directly, Burp Macros automatically perform a fresh login before every request. This bypasses the application's brute-force protection because each OTP attempt is made using a newly authenticated session.
