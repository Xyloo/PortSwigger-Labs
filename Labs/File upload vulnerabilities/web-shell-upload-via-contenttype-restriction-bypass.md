---
title: Web shell upload via Content-Type restriction bypass
category: File upload vulnerabilities
difficulty: Apprentice
tags:
  - insecure-design
  - file-upload
  - rce
---
A similar lab to [[Labs/File upload vulnerabilities/remote-code-execution-via-web-shell-upload|Remote code execution via web shell upload]], but with an extra restriction - the server has a whitelist of `Content-Type` headers. However, it may not verify whether the uploaded file actually matches the declared type.

Let's try to upload a web shell but with `Content-Type: image/jpeg`.
To do that, we select the PHP web shell file and turn on Intercept in Burp - to change the Content-Type to `image/jpeg` before it reaches the server.
![[web-shell-upload-via-contenttype-restriction-bypass-img1.png]]

The upload appears to succeed, but we will need to check:
![[web-shell-upload-via-contenttype-restriction-bypass-img2.png]]

The command given in the parameter has executed, therefore we have successfully uploaded a web shell.
![[web-shell-upload-via-contenttype-restriction-bypass-img3.png]]
