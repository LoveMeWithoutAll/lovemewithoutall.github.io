---
layout: single
title: "Fixing trailing slash redirects that drop to HTTP in an L4 load balancer + Nginx setup"
date: 2025-06-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://nginx.org/img/nginx_logo_dark.png"
    image: "https://nginx.org/img/nginx_logo_dark.png"
categories:
- IT
tags: [http, nginx]
---

# Fixing trailing slash redirects that drop to HTTP in an L4 load balancer + Nginx setup

**Summary**

In a setup where an L4 load balancer terminates TLS and Nginx only receives plain HTTP requests,
a single line, `absolute_redirect off;`, cleanly resolved the mixed HTTPS ↔ HTTP and 404 problem.

The key insight is this fact: "If the `Location` header is written as a relative path, the RFC dictates that the browser inherits the current scheme (HTTPS)."

---

## 1. Service Architecture

```
Client (HTTPS)
      │
┌───────────────┐
│  L4  Load     │  ← TLS termination
│  Balancer     │
└───────────────┘
      │  (HTTP)
┌───────────────┐
│     Nginx     │  ← receives plain HTTP
└───────────────┘
```

* **TLS verification**: handled by the L4 (Layer-4) appliance
* **Nginx**: receives plain HTTP only on port 80
* **Service requirement**: **all clients must access via HTTPS**.

---

## 2. Reproducing the Symptom

1. A user requests `https://domain/path` (no trailing `/`)
2. Nginx treats it as a directory → emits a 301 redirect
3. **`Location: http://domain/path/`**
4. The browser re-requests over HTTP → the L4 has no TLS → the backend can't find a particular resource and returns **404**
5. The user is baffled: "Why did my HTTPS page suddenly break?"

---

## 3. Digging into the Cause

### 3-1. Why was `http://` emitted?

* The L4 → Nginx leg is **plain HTTP**
* Nginx's internal `$scheme` variable is **always `http`**
* During an automatic `trailing slash` redirect, Nginx builds an absolute URL = `$scheme://$host/...`
  → as a result, **`http://`** gets baked in

### 3-2. Why it even goes as far as a 404

* Our service has no HTTP endpoint (or it's blocked by the WAF)
* The browser re-requests over HTTP → 404 or 403

---

## 4. The Fix ― a One-Line Setting

```nginx
# in nginx-vhosts.conf or the server block
absolute_redirect off;
```

* Forces Nginx to emit the redirect `Location` value as a **relative path** (`/pages/product-reg/`) only
* Since it never uses `$scheme` and the like, it **prevents http/https confusion**

---

## 5. "Why does this work?" — Because of RFC 7231

> **RFC 7231 §7.1.2 Location**
> "If the `Location` value is in relative URI form, the final value is resolved against the **effective request URI**."

In other words,

* When the client (browser) receives a relative `Location` (`/path/`),
  **"The URL I just sent was `https://…`, so I'll just append this to it."**
* So the HTTPS scheme and host are **inherited automatically by the client**
* Resulting redirect: `https://domain/path/`
* Testing with curl shows the same behavior

---

## 6. Packet Flow Summary, Before and After

| Stage                | Before                  | After             |
| -------------------- | ----------------------- | ----------------- |
| Client → L4          | HTTPS                   | HTTPS             |
| L4 → Nginx           | HTTP                    | HTTP              |
| Nginx 301 response   | `Location: http://…`    | `Location: /…`    |
| Browser re-request   | HTTP (↓ downgrade)      | HTTPS (scheme inherited) |
| Final response       | 404                     | 200               |

---

## 7. Practical Tips

1. **Local/Dev environments**
   * Even without HTTPS, a relative `Location` causes no problems

2. **Multiple domains**
   * `absolute_redirect off;` is safe without swapping per-host certificates

---

## 8. Wrapping Up — One-Line Summary

**In an L4-TLS-termination environment, the HTTPS→HTTP redirect caused by Nginx misjudging `$scheme`
can be cleanly resolved by forcing a relative `Location` header with `absolute_redirect off;`.**

The standard (RFC 7231) already guarantees the browser-side "protocol and domain inheritance" behavior, so you can use it with confidence.

---

### References

* **RFC 7231** – [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.2)
* **RFC 3986** – Uniform Resource Identifier (URI): Generic Syntax
* Nginx docs – [`absolute_redirect`](https://nginx.org/en/docs/http/ngx_http_core_module.html#absolute_redirect)


-----

EOD

20250604
