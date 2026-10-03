# Lab: Insecure Direct Object References

## Objective:
Retrieve Carlos's password from the chat transcripts and log into his account.

--- 

## Steps:

1. I opened the Live Chat feature on the website.

2. While interacting with the chat, I monitored the requests in Burp Suite.

3. I noticed the application requesting chat transcripts using static file paths such as:

GET /download-transcript/2.txt

GET /download-transcript/3.txt

4. Since the transcript files were referenced using sequential numbers, I suspected an Insecure Direct Object Reference (IDOR).

5. I modified the request manually by changing the file number:

GET /download-transcript/3.txt

to

GET /download-transcript/1.txt

6. After forwarding the request, the server returned another user's chat transcript.

7. Inside the transcript, I found Carlos's credentials, including his password:

Password:
3vzoodmns2zup8j42y1d

8. I logged into Carlos's account using the disclosed password.

9. The lab was successfully solved.

--- 

## Vulnerability:
Insecure Direct Object Reference (IDOR)

## Cause:
The application stored chat transcripts as files with predictable names (1.txt, 2.txt, 3.txt, etc.) and allowed users to access them directly through the URL without verifying ownership. By simply changing the file identifier, an attacker could access transcripts belonging to other users.

## Takeaway:
Whenever you encounter URLs containing predictable object references such as:

- /download/1
- /download/1.txt
- /files/123
- /invoice/1001
- /image/25

Always try modifying the identifier to access other resources.

Applications should never rely on predictable object references alone. Every request must include proper server-side authorization checks to ensure the authenticated user is permitted to access the requested resource.
