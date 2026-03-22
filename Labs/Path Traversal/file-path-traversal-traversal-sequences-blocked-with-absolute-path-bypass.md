---
title: File path traversal, traversal sequences blocked with absolute path bypass
category: Path traversal
difficulty: Practitioner
tags:
  - broken-access-control
  - path-traversal
---
This is a similar lab to [[Labs/Path Traversal/file-path-traversal-simple-case|File path traversal, simple case]], but the basic payload is blocked:
![[file-path-traversal-traversal-sequences-blocked-with-absolute-path-bypass-img1.png]]

We need to try a different approach.

The lab title and description mention including the file with an absolute path - but I'll try a different relative path payload first (`....//....//....//etc/passwd`) since the application (or WAF) might only block `../`

It did not work:
![[file-path-traversal-traversal-sequences-blocked-with-absolute-path-bypass-img2.png]]

The suggested payload `/etc/passwd` does in fact work:
![[file-path-traversal-traversal-sequences-blocked-with-absolute-path-bypass-img3.png]]
