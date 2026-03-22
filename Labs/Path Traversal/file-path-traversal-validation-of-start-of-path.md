---
title: File path traversal, validation of start of path
category: Path traversal
difficulty: Practitioner
tags:
  - broken-access-control
  - path-traversal
---
The lab title and description hint that a valid (expected) path at the beginning is required.

Searching the HTML source for `<img>` tags shows an interesting path to an image:
```html
<img src="/image?filename=/var/www/images/8.jpg">
```

It is a full, absolute path to an image. We can determine that the required path at the beginning (prefix) is most likely `/var/www/images`.

Let's add the payload `../../../etc/passwd` to the required path to form `/var/www/images/../../../etc/passwd`, which will satisfy the start of path check and still resolve to `/etc/passwd`.
![[file-path-traversal-validation-of-start-of-path-img1.png]]
