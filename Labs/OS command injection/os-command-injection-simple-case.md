---
title: OS command injection, simple case
category: OS command injection
difficulty: Apprentice
tags:
  - injection
  - os-command-injection
---
The lab requires us to find a place where we can possibly inject an OS command. The lab description mentions the stock checker. Nothing is immediately obvious as an entry point.
![[os-command-injection-simple-case-img1.png]]

The JS files do not provide any hints, therefore we can hope that the `productId` is vulnerable and passed directly to a shell. 

We can try a simple payload `2;whoami;`
![[os-command-injection-simple-case-img2.png]]

In the response we can see shell errors - which indicate that parameter values are passed through directly to a command on the server. The `whoami` command did not run though - it was probably due to using `;` instead of `&&` for command chaining - but the point is proven and the lab is solved regardless.