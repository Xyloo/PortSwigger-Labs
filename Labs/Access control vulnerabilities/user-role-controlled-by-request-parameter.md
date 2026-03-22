---
title: User role controlled by request parameter
category: Access control vulnerabilities
difficulty: Apprentice
tags:
  - broken-access-control
  - privilege-escalation
---
The lab description mentions the possibility of manipulating a user-controlled value to gain administrator privileges.
The credentials for the user account are `wiener:peter`.

Let's log in.
![[user-role-controlled-by-request-parameter-img1.png]]

After logging in, we can see the `id` parameter matches the username we logged in with.
We can try to alter this parameter to access the admin panel and delete the user  `carlos`, but this does **not** work. The website redirects us to the login page if the `id` parameter does not match the username we logged in with.

However, looking at the cookie, we can see `Admin=false;`.

![[user-role-controlled-by-request-parameter-img2.png]]

Let's alter the cookie to `Admin=true`.

After refreshing the page with `Admin=true`, an admin panel appears (which looks the same like as in [[Labs/Access control vulnerabilities/unprotected-admin-functionality|Unprotected Admin Functionality]]), where we can delete `carlos`.
![[user-role-controlled-by-request-parameter-img3.png]]
