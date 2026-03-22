---
title: User ID controlled by request parameter with password disclosure
category: Access control vulnerabilities
difficulty: Apprentice
tags:
  - broken-access-control
  - idor
---
Similarly to [[Labs/Access control vulnerabilities/user-id-controlled-by-request-parameter-with-unpredictable-user-ids|User ID controlled by request parameter, with unpredictable user IDs]], we need to exploit an IDOR vulnerability to gain admin access and delete the user `carlos`.

There is a password change form on `/my-account`:
![[user-id-controlled-by-request-parameter-with-password-disclosure-img1.png]]

It leaks the user's password in plaintext.
The GET parameter is also tied to the username.

Changing the GET parameter to `administrator` gives us access to their password. The password is masked, but all we need to do to reveal it is change `type=password` to `type=text` in DevTools or inspect the response in Burp.
![[user-id-controlled-by-request-parameter-with-password-disclosure-img2.png]]
Therefore, we can now log in as admin and delete `carlos`.