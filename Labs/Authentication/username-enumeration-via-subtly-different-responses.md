---
title: Username enumeration via subtly different responses
category: Authentication
difficulty: Practitioner
tags:
  - authentication-failures
  - brute-force
  - burp-pro-bypass
---
This lab is very similar to [[Labs/Authentication/username-enumeration-via-different-responses|Username enumeration via different responses]] but with an additional challenge - the response does not clearly reveal whether the username or password is incorrect.

Using Intruder, as in the lab linked above, shows that the response length varies slightly between requests, which makes it unreliable as an indicator of a valid username:
![[username-enumeration-via-subtly-different-responses-img1.png]]

Burp Community does not provide an easy way to filter responses by a specific term, which makes it impractical to identify the odd response manually across a large wordlist.

To work around this, I used Logger++ and exported the Intruder responses to a CSV file. I then opened the results in Sublime Text, whose search function supports multiline matching. By selecting a large constant portion of the HTML response, I was able to identify the response that differed.

The key difference was that responses for invalid usernames ended with a dot, while one response did not. That subtle variation revealed the valid username. In my case, it was `affiliate`.

The next step was to run Intruder again, this time to brute-force the password for that account. My original lab instance expired before I finished this stage, so the regenerated instance used a different username, but the method remained the same. After finding the correct password, the lab was solved.