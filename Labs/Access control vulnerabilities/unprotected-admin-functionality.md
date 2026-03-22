---
title: Unprotected admin functionality
category: Access control vulnerabilities
difficulty: Apprentice
tags:
  - broken-access-control
  - information-disclosure
---
A simple access control lab. The goal is to locate the admin panel and delete the user `carlos`.

Checking `robots.txt` reveals a disallowed path:
```http
User-agent: *
Disallow: /administrator-panel
```

This exposes the admin interface location. Browsing to `/administrator-panel` gives access to the panel, where we can delete `carlos`. The panel does not require any authentication.

![[unprotected-admin-functionality-img1.png]]
