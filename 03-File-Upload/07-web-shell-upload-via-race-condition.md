# Lab: Web Shell Upload via Race Condition

## Objective:
Exploit a race condition in the file upload process to execute a PHP web shell before the application validates and deletes it, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I intercepted the avatar upload request using Burp Suite.

3. I changed the uploaded filename to:

shell.php

4. I uploaded the following PHP payload:
```php
<?php system('cat /home/carlos/secret'); ?>
```
5. The vulnerable upload logic was:

move_uploaded_file()

↓

Virus Scan

↓

File Extension Check

↓

unlink()

This means the uploaded file was temporarily stored inside the avatars directory before the security checks were completed.

6. I created a Burp Repeater Group containing two requests:

Request #1

POST /my-account/avatar

to upload the PHP shell.

Request #2

GET /files/avatars/shell.php

to access the uploaded file.

7. I tested the available sending modes:

- Send group (separate connections) ❌
- Send group (parallel) ✅
- Send group (single connection) ❌

The successful mode was:

Send group (parallel)

8. I repeatedly sent the Repeater Group.

The first one or two attempts usually failed because the file had either not been written yet or had already been deleted.

After a few attempts, one of the GET requests reached the server during the small time window between:

move_uploaded_file()

and

unlink()

9. During that brief window, the uploaded PHP file still existed inside the avatars directory, so the server executed it before it was deleted.

10. The response returned Carlos's secret:

(Your Secret)

11. I copied the secret and submitted it.

M72Cub2KUxYCEx3eAOFVzXxCaIBuDAK3

The lab was successfully solved.

--- 

## Vulnerability:
Web Shell Upload via Race Condition

## Cause:
The application stored the uploaded file on disk before performing any security validation.

The upload process was:

1. Save uploaded file.
2. Scan for viruses.
3. Validate the extension.
4. Delete the file if validation failed.

Because the file existed temporarily before validation completed, an attacker could request it during this short time window and execute the PHP payload before it was removed.

This is a classic Time-of-Check to Time-of-Use (TOCTOU) Race Condition.

--- 

## Takeaway:

Race Conditions occur when two operations access the same resource at nearly the same time and the application's security depends on their execution order.

## In this lab:

- The upload request created the file.
- The GET request attempted to execute it.
- The validation process attempted to delete it.

Whichever operation reached the file first determined the outcome.

## Applications should:

- Validate files before storing them inside a web-accessible directory.
- Store uploads in a temporary non-public location until all validation is complete.
- Never expose uploaded files before security checks finish.
- Avoid TOCTOU race conditions by performing validation before making resources accessible.

Even a very small execution window can be enough to achieve Remote Code Execution when requests are sent simultaneously.
--------------------------------------------------------------------------------
# Alternative Solution (Official PortSwigger Approach)

## PortSwigger recommends solving this lab using the Turbo Intruder extension.

## Steps:

1. Log in using:

Username: wiener
Password: peter

2. Prepare a PHP web shell:
```php
<?php system('cat /home/carlos/secret'); ?>
```
3. Intercept the avatar upload request and save it.

4. Send the upload request to Turbo Intruder.

5. Configure Turbo Intruder to continuously upload the PHP file while simultaneously sending repeated GET requests for:

GET /files/avatars/shell.php

6. Turbo Intruder sends a very large number of requests concurrently, significantly increasing the chance that one GET request reaches the server after:

move_uploaded_file()

but before:

unlink()

7. When one request wins the race, the PHP payload is executed before the application deletes the uploaded file.

8. The response returns Carlos's secret, which is then submitted to solve the lab.

--- 

## Why Turbo Intruder Works:

Turbo Intruder is designed for high-speed HTTP attacks.

## It can:

- Send thousands of requests per second.
- Maintain concurrent connections.
- Precisely synchronize requests.
- Increase the probability of winning race conditions.

This makes it particularly useful for exploiting TOCTOU (Time-of-Check to Time-of-Use) vulnerabilities like this lab.
