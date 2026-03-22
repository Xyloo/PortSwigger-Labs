---
title: User ID controlled by request parameter, with unpredictable user IDs
category: Access control vulnerabilities
difficulty: Apprentice
tags:
  - broken-access-control
  - idor
---
Since the application uses GUIDs for user IDs, it would be impossible to guess `carlos`'s ID directly.

Our goal is to find `carlos`'s GUID and use an IDOR vulnerability to leak their API key.
Like in [[Labs/Access control vulnerabilities/user-role-controlled-by-request-parameter|User role controlled by request parameter]] we are given credentials for `wiener`.

The `/my-account` page has a GET parameter for the user ID - which strongly suggests there might be an IDOR vulnerability:
`GET /my-account?id=da0fe4d0-(...)-348490b974b7 HTTP/2`

Now we need to find another user's GUID and see if we get access to their version of `/my-account` page.

The website is a blog. Looking at one of the posts shows that the author's GUID is used in the link for viewing their other articles.
![[user-id-controlled-by-request-parameter-with-unpredictable-user-ids-img1.png]]

Therefore, we need to find an article written by `carlos`.

After a bit of browsing, we find `carlos`'s GUID.
![[user-id-controlled-by-request-parameter-with-unpredictable-user-ids-img2.png]]

Let's try to leak his API key with the previous (possible) IDOR.
![[user-id-controlled-by-request-parameter-with-unpredictable-user-ids-img3.png]]

The IDOR works - we can now see `carlos`'s API key.
