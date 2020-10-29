## What is jigsaw?

_UI composition layer_

- NGINX web server <!-- .element: class="fragment" data-fragment-index="0" -->
- Micro frontend`s with SSI (Server Side Include) <!-- .element: class="fragment" data-fragment-index="1" -->
- Fragment and asset caching <!-- .element: class="fragment" data-fragment-index="2" -->
- Google Pagespeed module <!-- .element: class="fragment" data-fragment-index="3" -->

<!-- vertical -->

### How it works

![Jigsaw SSI flow](ssi-flow.svg) <!-- .element: style="width:100%; background-color:#fff" -->

<!-- section -->

## The cookie problem

![cookie monster](cookiemonster.gif)

<!-- vertical -->

### Cookies at AutoScout24

_There is still a scrollbar..._

![Jigsaw SSI flow](cookies.png)

<!-- vertical -->

### Why cookies crash the pages

- Request with large cookies reaches different services
- Each fragment request can lead to a broken page
- Different max header size limit per service

<!-- vertical -->

### Cookie size != request size

A typical browser request includes the request URI and multiple headers

`request line (80 bytes) + header (base 335 bytes + cookie 4500 bytes (home page))`

```ht
GET /lst?lm_premium=true&sort=standard&desc=0&ustate=N%2CU&cy=D&atype=C HTTP/1.1
<METHOD> <URI> <HTTP VERSION>
+
Host: www.autoscout24.de
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:82.0) Gecko/20100101 Firefox/82.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: de-DE,de;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: br, gzip, deflate
DNT: 1
Upgrade-Insecure-Requests: 1
Connection: keep-alive
+
Cookie: optimizelyEndUserId=o............
```

<!-- section -->

### Known http/1.x header limits

- AWS Cloudfront: Total request limit of 20 K (path + query string + headers) <!-- .element: class="fragment" data-fragment-index="0" -->
- S3: Total header request limit of 8192 bytes <!-- .element: class="fragment" data-fragment-index="0" -->
- AWS ELB: single header: 16 K, whole header: 64 K <!-- .element: class="fragment" data-fragment-index="0" -->
- NGINX/Jigsaw: request headers up to 21 K <!-- .element: class="fragment" data-fragment-index="1" -->
- NodeJS: Default max header limit of 8192 bytes <!-- .element: class="fragment" data-fragment-index="2" -->
- Play framework: Defaults to 8192 bytes (not validated) <!-- .element: class="fragment" data-fragment-index="2" -->

> There might be different limits for header size and request URI size

<!-- vertical -->

## Mitigation

- Jigsaw header limits is aligned with cloudfront limit (**21k**)
- Jigsaw has been configured to prevent sending cookie headers to **S3**
- Avoid setting cookies at **root path** `/`!
- Increase the accepted max header request size
- Anything else to avoid blowing the cookies

<!-- section -->

## HTTP/2

Jigsaw/Nginx does only make `http/1.x` calls to the proxy upstream/backend. All requests between Cloudfront, jigsaw and the specific backend should remain with `http/1.x`.

```text
Browser
http/2 | http/1.x
    -> Cloudfront
       http/1.x
          -> AWS Classic ELB
             http/1.x
              -> Jigsaw/Nginx
                 http/1.x
                    -> AWS ELB
                       http/1.x
                        -> service application
```

<!-- vertical -->

### Cookies in HTTP/2

**HTTP/2** and the Cookie header field is treated specially by the HTTP mapping ([see RFC 7540 Section 8.1.2.5](https://tools.ietf.org/html/rfc7540#section-8.1.2.5)).

> To allow for better compression efficiency, the Cookie header field MAY be split into separate header fields, each with one or more cookie-pairs

<!-- section -->

# Thank you!

**Richard Hillmann**

_Senior Platform Engineer_
