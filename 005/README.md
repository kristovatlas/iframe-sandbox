## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.
This uses this CSP header: `Content-Security-Policy: default-src 'none'; child-src 'self' http://you.cannot.win:9337 ;`
This uses this sandbox attribute: `sandbox`

## Prerequisites

Add to `/etc/hosts` file:
```
127.0.0.1       all.i.do.is.win
127.0.0.1       got.money.on.my.mind
```

## Usage

`python server.py`

In your browser, visit: http://all.i.do.is.win:9337/

## Summary of results

Successfully loaded the iframe, but blocked all script-related things it wanted to accomplish because of a combination of CSP and iframe sandbox.

## Results in various browsers

OSX/Google Chrome 52.0:
```
Refused to execute inline script because it violates the following Content Security Policy directive: "default-src 'none'". Either the 'unsafe-inline' keyword, a hash ('sha256-R1WSspSmoxU9rGDX08R+aaprTREEUWr3i6Odp2Y1Szg='), or a nonce ('nonce-...') is required to enable inline execution. Note also that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

all.i.do.is.win/:1 Refused to load the script 'http://all.i.do.is.win:9337/remote.js' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

all.i.do.is.win/:1 Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

compromised.html:1 Blocked script execution in 'http://you.cannot.win:9337/compromised.html' because the document's frame is sandboxed and the 'allow-scripts' permission is not set.
compromised.html:1 Blocked script execution in 'http://you.cannot.win:9337/compromised.html' because the document's frame is sandboxed and the 'allow-scripts' permission is not set.
all.i.do.is.win/:9 Refused to execute inline event handler because it violates the following Content Security Policy directive: "default-src 'none'". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution. Note also that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.
```
