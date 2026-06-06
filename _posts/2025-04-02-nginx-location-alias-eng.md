---
layout: single
title: On the Case of the 404 Error That Kept Showing Up No Matter How Many Times I Redid the nginx location Settings
date: 2025-04-02 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://nginx.org/img/nginx_logo_dark.png"
    image: "https://nginx.org/img/nginx_logo_dark.png"
categories:
- IT
tags: [nginx]
---

## The Situation
* Using yarn workspaces
* The goal is to generate a build output for each package and deploy them as js files, micro-frontend style
* Example build output file location: `/docroot/packages/footer/dist/footer.js`
* Example js file deployment url: `https://domain/footer/footer.js`
* No matter how I change the `http.server.location` setting in the nginx.conf file, I can't access the build output file and get a 404 error

### The configuration that produces the 404 error

```nginx
# nginx.conf
http {
  server {
    location /footer {
      root /docroot/packages/footer/dist/; # the build output of the footer package
      index footer.js; # the setting intended to deploy footer.js
      try_files $uri $uri/ /footer.js; # but all I get is a 404 error
    }
  }
}
```

## The Cause
It happens because I misunderstood how `http.server.location.root` and `http.server.location.alias` work. Let's look at how each setting behaves.

### 1. `http.server.location.root`

```nginx
# nginx.conf
http {
  server {
    location /footer {
      root /docroot/packages/footer/dist/; # the build output of the footer package
      index footer.js; # the setting intended to deploy footer.js
      try_files $uri $uri/ /footer.js; # I told it to look here on error, but the path just gets tangled up
    }
  }
}
```
When a request comes in to `https://domain/footer`, nginx computes the actual path as follows.

`root + the rest of the URI → /docroot/packages/footer/dist/footer`

nginx appends `/footer` after the configured root (`/docroot/packages/footer/dist/footer/footer`) and looks for the file there. So a 404 error is inevitable.

### 2. `http.server.location.alias`

With alias, you can ignore the path written in `http.server.location` within the url path and map it to a completely different directory instead.

```nginx
# nginx.conf
http {
  server {
    location /footer {
      alias /docroot/packages/footer/dist/; # a setting that still produces an error
      index footer.js;
      try_files $uri $uri/ /footer.js; # the path still gets tangled up
    }
  }
}
```

I thought using alias would solve it, but when you send a request to the `domain/footer` url, you get a `301 Moved Permanently` response and are automatically redirected to `/footer/`, which results in a 404 error. On the request that occurs after the 301 response, the location setting configured above no longer applies either, so it wanders around somewhere on the server and ends up responding with a 404 error.

Here's the reason. When you use alias as above and request `domain/footer` from the browser,

1.	Nginx <b>decides that /footer is a directory</b> and appends a slash (/) to redirect → 301 Moved Permanently occurs
2.	The browser re-requests `/footer/`
3.	The path `/docroot/package/footer/dist/footer/` does not exist, so a 404 error occurs

So can we solve it by specifying the path explicitly?

```nginx
# nginx.conf
http {
  server {
    location /footer/ { # added a / (slash) at the end of the path
      alias /docroot/packages/footer/dist/; # a setting that still produces an error
      index footer.js;
      try_files $uri $uri/ /footer.js;
    }
  }
}
```

It still produces nothing but a 404 error. The reason is that in nginx, alias doesn't simply swap the path. It removes the location path that came in via the url, then appends the remaining path after the alias target path to build the final file path.

For example, when it receives `request URL: domain/footer/footer.js`, nginx appends the path to `/docroot/packages/footer/dist/`, the `location.alias` path, and tries to serve the file `/docroot/packages/footer/dist/footer/footer.js`. Since that path doesn't exist, a 404 error occurs. As a result, because the path resolution ends up looking for `footer/footer.js`, the same problem as with `http.server.location.root` occurs.

Don't you feel like you have no idea what this means? Me too. There's no way to solve the problem like this. So let's cut out as much unnecessary behavior as possible and make it simpler.

## The Solution

The most suitable configuration for deploying a single file is as follows.

```nginx
location = /footer/footer.js {
  alias /docroot/packages/footer/dist/footer.js;
}
```

This configuration handles exactly one request, `domain/footer/footer.js`, so the ambiguous path resolution problem disappears. `request url: domain/footer/footer.js` maps to exactly one file, the alias path `/docroot/packages/footer/dist/footer/footer.js`. When you use `location =`, nginx applies the setting only to that exact request, and because alias specifies the entire path directly, there's no room for path conflicts or resolution errors.

It neither appends the url path after nginx's root path like `http.server.location.root`, nor replaces the url path with nginx's alias value like `http.server.location.alias`.


## Conclusion

*	`http.server.location.root` builds the actual file path by appending the entire request url after the root path (configured in nginx).
*	`http.server.location.alias` <b>removes the path matching the location path (configured in nginx) from the request path (url path), then appends the rest after the alias path</b>.
*	The position of the slash (/) is very important, and you need to include the trailing slash, like `location /foo/`, for accurate mapping.
*	For a single file, the location = approach is the most reliable.

Let's build a mental model.

### root
- nginx works like this with root: "When you, the browser, say /footer/, I'll take the /docroot/.../dsit/ path and append exactly what you said, /footer/ and the rest after it."
- root: looks up [nginx root] + [url path] on the server.

```
[ Request URL: /static/app.js ]
           ↓
[ location /static/ ]
           ↓
[ root /var/www/ ]
           ↓
[ Result Path: /var/www/static/app.js ]
```

### alias
- nginx works like this with alias: "When you, the browser, say /footer/, I'll reinterpret it as /docroot/.../dist/. And I'll just append the rest after /footer/ as-is."
- alias: looks up [nginx alias] - [nginx location] + [url path] on the server.

```
[ Request URL: /static/app.js ]
           ↓
[ location /static/ ]
           ↓
[ alias /var/www/assets/ ]
           ↓
[ Result Path: /var/www/assets/app.js ]
```

20250402
