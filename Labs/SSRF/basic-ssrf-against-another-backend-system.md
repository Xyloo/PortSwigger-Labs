---
title: Basic SSRF against another back-end system
category: SSRF
difficulty: Apprentice
tags:
  - broken-access-control
  - ssrf
  - scanning
---
This is a similar lab to [[Labs/SSRF/basic-ssrf-against-the-local-server|Basic SSRF against the local server]] but we this time we need to scan for a device in the ``192.168.0.X`` range on port `8080` and delete the user `carlos`.

Since this is a job best suited for Intruder, we can use it together with the payload from the lab linked above.

The `<select>` element shows that the application uses a full URL for the stock API:
```html
<select name="stockApi">
	<option value="http://192.168.0.1:8080/product/stock/check?productId=2&storeId=1">London</option>
	<option value="http://192.168.0.1:8080/product/stock/check?productId=2&storeId=2">Paris</option>
	<option value="http://192.168.0.1:8080/product/stock/check?productId=2&storeId=3">Milan</option>
</select>
```

We can use Intruder to scan the network for the correct host.

To set this up, we replace the last IP octet with a payload position and use sequential numbers from `1` to `255`. The payload is also prepared to call the deletion endpoint for `carlos`, which speeds things up.
![[basic-ssrf-against-another-backend-system-img1.png]]

We got a redirection code with the payload `110`, so the correct address is `192.168.0.110`.
![[basic-ssrf-against-another-backend-system-img2.png]]
