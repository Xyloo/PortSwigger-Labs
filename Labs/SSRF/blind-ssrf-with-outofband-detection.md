---
title: Blind SSRF with out-of-band detection
category: SSRF
difficulty: Practitioner
tags:
  - broken-access-control
  - ssrf
  - burp-pro-bypass
---
The lab description states that Burp Collaborator should be used. However, Collaborator is not available in the Community version.

We can try to bypass that restriction.

The description mentions to put the payload in the `Referer` header. Since we cannot use Collaborator directly, we can try to use a bogus subdomain on `burpcollaborator.net` and see if it solves the lab.
![[blind-ssrf-with-outofband-detection-img1.png]]

At first glance, it did not (shows up as "Not solved" in the response) - however checking the Academy page again shows the lab was actually solved.
![[blind-ssrf-with-outofband-detection-img2.png]]

The point of this lab is to show that blind SSRF does not expose the result of the request directly in the response. Instead, we have to rely on an out-of-band interaction triggered by the server. Of course, without access to Collaborator or our own domain, it is impossible to observe the actual interaction unless the SSRF causes some visible change in application state that we can verify separately.