---
layout: single
title: Applying msw (mock service worker) to a webpack project
date: 2022-01-19 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://github.com/mswjs/msw/raw/main/media/msw-logo.svg"
    image: "https://github.com/mswjs/msw/raw/main/media/msw-logo.svg"
categories:
- IT
tags: [etc]
---

## msw (mock service worker)
What is msw? A service worker that mocks API responses.

## Implementation issues
- msw constraints
	- msw's mockServiceWorker only runs properly in an https environment. (When I checked again, it turns out it runs over http too...)
- Project constraints
	- To integrate with other systems (the authentication server), the URL in the local development environment must be set to *.ourcompanyurl.co.kr.
- webpack-dev-server constraints
	- webpack-dev-server's SSL certificate uses 'localhost' as its host.
	- (Since this is a project that uses React) create-react-app's webpack-dev-server runs with 'localhost' as its host (default setting).

## Steps taken
1. TLS certificate
	1. Create a TLS certificate for the *.ourcompanyurl.co.kr domain.
	2. Create a /scripts directory under the project's root path.
	3. Under the directory above, create a make_cert.sh file with the following contents.
	4. Run the shell script below.

```sh
## make_cert.sh

CONFIGDIR="ssl/"
# set /etc/hosts
# Proceed assuming the host configuration is already complete.
# install mkcert
brew install mkcert
# create local CA & system trust store
mkcert -install
# create certificate
mkdir -p $CONFIGDIR
cd $CONFIGDIR
mkcert live11-local.ourcompanyurl.co.kr
```

   
2. webpack-dev-server
	1. Change webpack-dev-server's host to *.ourcompanyurl.co.kr.
		1. Add the following setting to the env file

```sh
# .env file
HOST=live11-local.ourcompanyurl.co.kr
```

   2. Configure webpack-dev-server to run over HTTPS.
      1. Add the following setting to the env file
```sh
# .env file
HTTPS=true
```
   1. Replace webpack-dev-server's TLS certificate with the certificate created above.
		1. Register the following script in package.json
		2. The following script always runs before the start command.
```json
"prestart": "rm ./node_modules/webpack-dev-server/ssl/server.pem && cp -f ./ssl/server.pem ./node_modules/webpack-dev-server/ssl"
```

Now if you run yarn start or npm start, msw should run just fine. The end.

20220119
