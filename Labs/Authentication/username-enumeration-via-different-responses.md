---
title: Username enumeration via different responses
category: Authentication
difficulty: Apprentice
tags:
  - authentication-failures
  - brute-force
---
The goal of this lab is to enumerate valid usernames from a given wordlist based on differences in the server's response.

Let's try a few usernames from the wordlist and compare the differences.
![[username-enumeration-via-different-responses-img1.png]]

The application leaks whether the username is valid. Instead of returning a generic message such as `Invalid username or password`, it reveals which field is incorrect.

Using Intruder with the username list produces one distinct response for `username=arcsight` where the response length differs from the others. In that case, the application returns `Invalid password` instead of `Invalid username`, which confirms that `arcsight` is a valid username. The next step is to use Intruder again, this time to brute-force the password.
![[username-enumeration-via-different-responses-img2.png]]

The password is `trustno1` and we managed to log in successfully.
![[username-enumeration-via-different-responses-img3.png]]
