---
title: Web Cache Poisoning part-1
date: 2025-10-02 12:00:00 +0530
categories: [portswigger]
tags: [web cache poisoning]
---
# Web Cache Poisoning 

### 1.Web cache poisoning with an unkeyed header 

First we add a cache buster so that we wouldnt effect any live users in this case we could inject into query parameter

```
GET /?cb=something1
```

Then we should identify if it has cache oracle or not and we could confirm it by 
these headers in response when we change cache buster
```
Cache-Control:max-age=30
Age: 30
X-Cache: hit
```
Now we have to send it to param miner to get any unkeyed headers.so that we can poison cached data and we identified one

```
Identified parameter on 0a2400b6034a6efc8043038000570045.web-security-academy.net: x-forwarded-host
```
Now we have to observe whether our input in x-fowarded-host is used or not in response and it is reflected in the following script tag

```
<script type="text/javascript" src="//exploit-0abf00a903856e8a800c025601140015.exploit-server.net/resources/js/tracking.js"></script>
```
So we change File location of exploit server to this '/resources/js/tracking.js' and body with 'alert(document.cookie);' as payload then we remove cache buster and directly inject into the main page and we get 

![img-description](/assets/assets/img/cache1.png)


### 2.Web cache poisoning with an unkeyed cookie

First we add a cache buster so that we wouldnt effect any live users in this case we could inject into query parameter

```
GET /?cb=something1
```
Then we should identify if it has cache oracle or not and we could confirm it by 
these headers in response when we change cache buster
```
Cache-Control:max-age=30
Age: 30
X-Cache: hit
```
Now we have to send it to param miner to get any unkeyed headers.so that we can poison cached data and we identified one
```
Identified parameter on 0a5b008504de744181685cf0002f0077.web-security-academy.net:
fehost,cookie
```
After finding parameters let's test whether it is reflecting and it is reflected in response.so i designed a payload the sequence
```
"}</script><script>alert(1)</script>{"
```
we got xss
![img-description](/assets/assets/img/cache2.png>)
### 3.Web cache poisoning with multiple headers

Here we can add cache buster directly to header

Now my cache buster is working and giving responses in with cache misses when i am adding different cache busters

sending it to param miner giving following header
```
x-forwarded-scheme
```
If any value other than https is given to this it is giving a 302 redirect.

Param miner works by change of response when testing header is sent and when it is not.As we are getting a redirect we could find other headers when x-forwarded-scheme is used

so again sending this request to param miner giving 
```
x-forwarded-host
```
