---
title: SSRF with blacklist-based input filter
category: SSRF
difficulty: Practitioner
tags:
  - broken-access-control
  - ssrf
---
The lab description mentions that there might be filters on `/admin`, `localhost`, `127.0.0.1` and similar values.

There are several methods to try and bypass such filters:
- URL encoding
- case variation, in case only `/admin` is matched, but `/ADmiN` isn't
- alternative IP representations such as `2130706433`, `017700000001`, `127.1` for `127.0.0.1`
- using a domain with DNS resolving to `127.0.0.1` such as `spoofed.burpcollaborator.net`

Checking the naive approach `/admin` returns an error message:
![[ssrf-with-blacklistbased-input-filter-img1.png]]

The request was blocked. We can try an alternative `localhost` representation (`127.1`):
![[ssrf-with-blacklistbased-input-filter-img2.png]]

This still does not work, so there is probably a filter on `/admin`. We can try to alter casing in `/admin` to `/adMiN`:
![[ssrf-with-blacklistbased-input-filter-img3.png]]

The server returns the admin panel - now we need to delete the user by appending `/delete?username=carlos`:
![[ssrf-with-blacklistbased-input-filter-img4.png]]