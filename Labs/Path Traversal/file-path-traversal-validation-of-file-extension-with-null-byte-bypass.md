---
title: File path traversal, validation of file extension with null byte bypass
category: Path traversal
difficulty: Practitioner
tags:
  - broken-access-control
  - path-traversal
---
The lab title and description suggest that the application validates the end of the path to ensure it matches an allowed file extension. A common way to bypass this is with a null byte, which may be treated by the OS as the end of the string.

To check what extensions are allowed, we can look for an `<img>` tag like:
```html
<img src="/image?filename=31.jpg">
```
So we know the extension should be `.jpg`.

Let's try to bypass this with a null byte - `../../../etc/passwd%00.jpg`.
The application sees a path ending in `.jpg`, while the OS treats the null byte as the end of the string and resolves the path as `../../../etc/passwd`.

![[file-path-traversal-validation-of-file-extension-with-null-byte-bypass-img1.png]]
