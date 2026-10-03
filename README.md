# PortSwigger Web Security Academy

A collection of my write-ups, notes, and practical solutions while studying web application security through the **PortSwigger Web Security Academy**.

The repository documents the vulnerabilities I studied, the techniques I used to solve the labs, and the key concepts I learned throughout the process.

---

## Progress

| #  | Topic                                                      | Labs | Status       |
| -- | ---------------------------------------------------------- | ---: | ------------ |
| 01 | [Authentication](./01-Authentication/)                     |   14 | ✅ Completed |
| 02 | [Access Control](./02-Access-Control/)                     |   12 | ✅ Completed |
| 03 | [File Upload](./03-File-Upload/)                           |    7 | ✅ Completed |
| 04 | [OAuth](./04-OAuth/)                                       |    6 | ✅ Completed |
| 05 | [SSRF](./05-SSRF/)                                         |    7 | ✅ Completed |
| 06 | [XXE](./06-XXE/)                                           |    9 | ✅ Completed |
| 07 | [CORS](./07-CORS/)                                         |    3 | ✅ Completed |
| 08 | [CSRF](./08-CSRF/)                                         |   11 | ✅ Completed |
| 09 | [Clickjacking](./09-Clickjacking/)                         |    4 | ✅ Completed |
| 10 | [SQL Injection](./10-SQL-Injection/)                       |   18 | ✅ Completed |
| 11 | [WebSockets](./11-WebSockets/)                             |    3 | ✅ Completed |
| 12 | [Server-Side Template Injection](./12-SSTI/)               |    7 | ✅ Completed |
| 13 | [Insecure Deserialization](./13-Insecure-Deserialization/) |   10 | ✅ Completed |
| 14 | [Business Logic](./14-Business-Logic/)                     |   12 | ✅ Completed |
| 15 | [HTTP Request Smuggling](./15-HTTP-Request-Smuggling/)     |   21 | ✅ Completed |

**Total Labs Completed: 144**

---

## Topics Covered

### Authentication

Authentication vulnerabilities including:

* Username Enumeration via Different Responses
* 2FA Simple Bypass
* Password Reset Broken Logic
* Username Enumeration via Subtly Different Responses
* Username Enumeration via Response Timing
* Broken Brute-Force Protection, IP Block
* Username Enumeration via Account Lock
* 2FA Broken Logic
* Brute-Forcing a Stay-Logged-In Cookie
* Offline Password Cracking
* Password Reset Poisoning via Middleware
* Password Brute-Force via Password Change
* Broken Brute-Force Protection, Multiple Credentials per Request
* 2FA Bypass Using a Brute-Force Attack

### Access Control

* Unprotected Admin Functionality
* Unprotected Admin Functionality with Unpredictable URL
* User Role Controlled by Request Parameter
* User Role Can Be Modified in User Profile
* User ID Controlled by Request Parameter
* User ID Controlled by Request Parameter, with Unpredictable User IDs
* User ID Controlled by Request Parameter with Data Leakage in Redirect
* User ID Controlled by Request Parameter with Password Disclosure
* Insecure Direct Object References (IDOR)
* Method-Based Access Control Can Be Circumvented
* Multi-Step Process with No Access Control on One Step
* Referer-Based Access Control

### File Upload

* Web shell uploads
* Content-Type restriction bypass
* Path traversal
* Extension blacklist bypass
* Obfuscated file extensions
* Polyglot files
* Race-condition based file upload attacks

### OAuth

* OAuth implicit flow vulnerabilities
* OpenID Dynamic Client Registration
* Forced OAuth profile linking
* `redirect_uri` vulnerabilities
* Open redirect based token theft
* OAuth proxy page attacks

### SSRF

* Local server SSRF
* Internal backend systems
* Blind SSRF
* Blacklist bypasses
* Open redirect based SSRF bypass
* Shellshock exploitation
* Whitelist-based SSRF bypass

### XXE

* External entities
* XXE-based SSRF
* Blind XXE
* Parameter entities
* Malicious external DTDs
* Error-based data exfiltration
* XInclude
* XXE through image uploads
* Local DTD reuse

### CORS

* Basic origin reflection
* Trusted `null` origin
* Trusted insecure protocols

### CSRF

* Missing CSRF defenses
* Token validation flaws
* Session-independent tokens
* Cookie-based token flaws
* SameSite bypasses
* Referer validation weaknesses

### Clickjacking

* Basic clickjacking
* URL parameter injection
* Frame-buster bypass
* DOM-based XSS through clickjacking

### SQL Injection

* WHERE clause injection
* Authentication bypass
* Database identification
* Database enumeration
* UNION attacks
* Blind SQL injection
* Error-based SQL injection
* Time-based SQL injection
* Out-of-band SQL injection
* Filter bypass techniques

### WebSockets

* WebSocket message manipulation
* Cross-site WebSocket hijacking
* WebSocket handshake manipulation

### Server-Side Template Injection

* Basic SSTI
* Code context SSTI
* Documentation-based exploitation
* Unknown template languages
* Information disclosure
* Sandboxed environments
* Custom SSTI exploits

### Insecure Deserialization

* Modifying serialized objects
* Serialized data types
* Application functionality abuse
* PHP object injection
* Java deserialization
* PHP gadget chains
* Ruby deserialization
* Custom Java gadget chains
* Custom PHP gadget chains
* PHAR deserialization

### Business Logic

* Excessive trust in client-side controls
* High-level logic flaws
* Inconsistent security controls
* Flawed business-rule enforcement
* Low-level logic flaws
* Exceptional input handling
* Dual-use endpoint isolation
* Workflow validation
* State-machine vulnerabilities
* Infinite money flaws
* Encryption oracles
* Email parsing discrepancies

### HTTP Request Smuggling

* CL.TE desynchronization
* TE.CL desynchronization
* Front-end security control bypass
* Front-end request rewriting
* Capturing other users' requests
* Reflected XSS
* H2.TE request smuggling
* H2.CL request smuggling
* HTTP/2 CRLF injection
* HTTP/2 request splitting
* CL.0 request smuggling
* Basic CL.TE and TE.CL
* TE header obfuscation
* Web cache poisoning
* Web cache deception
* HTTP/2 request tunnelling
* Client-side desync
* Server-side pause-based request smuggling

---

## Methodology

Each write-up focuses on the practical process used to solve the lab, including:

* Vulnerability identification
* Request/response analysis
* Burp Suite techniques
* Payload construction
* Exploitation steps
* Important observations
* Root cause
* Key takeaways

Sensitive values such as session cookies, credentials, CSRF tokens, API keys, and other temporary lab secrets are redacted before publication.

---

## Disclaimer

All techniques documented in this repository were performed against authorized **PortSwigger Web Security Academy** labs for educational purposes.

The techniques should only be used against systems where you have explicit authorization to test.

---

## Repository Structure

```text
PortSwigger-Web-Security-Academy/
│
├── 01-Authentication/
├── 02-Access-Control/
├── 03-File-Upload/
├── 04-OAuth/
├── 05-SSRF/
├── 06-XXE/
├── 07-CORS/
├── 08-CSRF/
├── 09-Clickjacking/
├── 10-SQL-Injection/
├── 11-WebSockets/
├── 12-SSTI/
├── 13-Insecure-Deserialization/
├── 14-Business-Logic/
├── 15-HTTP-Request-Smuggling/
└── README.md
```
