---
title: Basic SSRF against the local server
category: SSRF
difficulty: Apprentice
tags:
  - broken-access-control
  - ssrf
---
The goal of this lab is to access the admin panel at `http://localhost/admin` and delete the user `carlos` . The stock checker is mentioned as a possible entry point.

From the frontend, we can trigger a stock check like this:
![[basic-ssrf-against-the-local-server-img1.png]]

This corresponds to a POST request, where we can see a URL to a stock checking API:
![[basic-ssrf-against-the-local-server-img2.png]]

This suggests that the application may be vulnerable to SSRF. We can test this by changing the `stockApi` parameter to `http://localhost/admin` (with URL encoding):
![[basic-ssrf-against-the-local-server-img3.png]]

The admin panel is returned in response. In the HTML, we can see the endpoint used to delete `carlos`:
```html
<a href="/admin/delete?username=carlos">
```

We need to update our payload accordingly so the user `carlos` gets deleted:
![[basic-ssrf-against-the-local-server-img4.png]]

After following the redirection, the lab is marked as solved.
![[basic-ssrf-against-the-local-server-img5.png]]

Technically the site returns `401 Unauthorized`, but since the lab is solved, our request did go through. We can confirm this by loading the admin panel again with the previous payload:
![[basic-ssrf-against-the-local-server-img6.png]]

The user `carlos` no longer appears in the list.