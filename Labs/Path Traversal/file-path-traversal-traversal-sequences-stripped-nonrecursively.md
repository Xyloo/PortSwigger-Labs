---
title: File path traversal, traversal sequences stripped non-recursively
category: Path traversal
difficulty: Practitioner
tags:
  - broken-access-control
  - path-traversal
---
This lab is similar to [[Labs/Path Traversal/file-path-traversal-traversal-sequences-blocked-with-absolute-path-bypass|File path traversal, traversal sequences blocked with absolute path bypass]], but this time the application strips traversal sequences non-recursively.

That means a payload such as `....//....//....//etc/passwd` is transformed into a valid traversal path after the first stripping pass, allowing us to read `/etc/passwd`.
![[file-path-traversal-traversal-sequences-stripped-nonrecursively-img1.png]]