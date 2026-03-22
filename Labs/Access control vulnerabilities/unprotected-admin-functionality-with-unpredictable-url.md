---
title: Unprotected admin functionality with unpredictable URL
category: Access control vulnerabilities
difficulty: Apprentice
tags:
  - broken-access-control
  - information-disclosure
---
A slight twist on [[Labs/Access control vulnerabilities/unprotected-admin-functionality|Unprotected Admin Functionality]]. We need to find the URL to the administrator's panel, although as the lab title says - it won't be in an easily predictable location.

Let's just search for `panel` in the page source.
![[unprotected-admin-functionality-with-unpredictable-url-img1.png]]

Going to the `/admin-4bsi5m` endpoint gives us the admin panel. Like in [[Labs/Access control vulnerabilities/unprotected-admin-functionality|Unprotected Admin Functionality]] the panel does not require any authentication.
![[unprotected-admin-functionality-with-unpredictable-url-img2.png]]