---
title: 2FA simple bypass
category: Authentication
difficulty: Apprentice
tags:
  - broken-access-control
  - 2fa
---
In this lab, we are given credentials for users `wiener` and `carlos`. The goal is to bypass the 2FA page.

Previous theoretical introduction (in Paths) mentioned that a user might achieve a logged-in state with just the password, without completing the 2FA challenge. This would give them access to the  authenticated pages when they should not have that access.

Let's first see how the process works for a normal user. We are given access to an email page for `wiener` which gives us the 2FA code required for login.

We now know that the default landing page after login is `/my-account?id=wiener`. The page is not exploitable via IDOR (changing the GET parameter will not give us access to other users' page).

Since we have the credentials for `carlos`, we'll log in as that user instead. On the 2FA page we change the URL from `/login2` to `/my-account?id=carlos`, which gives us the authenticated functionality and solves the lab.
![[2fa-simple-bypass-img1.png]]