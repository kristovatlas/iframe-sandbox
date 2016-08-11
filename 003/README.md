## Description

Demo of compromised 3rd party web page loaded in non-sandboxed iframe. This uses this CSP header: `Content-Security-Policy: default-src 'none'; child-src 'self'`

## Prerequisites

Add to `/etc/hosts` file:
```
127.0.0.1       all.i.do.is.win
127.0.0.1       you.cannot.win
```

## Usage

`python server.py`

In your browser, visit: http://all.i.do.is.win:9337/

## Results in various browsers

OSX/Google Chrome 52.0:
```
Refused to execute inline script because it violates the following Content Security Policy directive: "default-src 'none'". Either the 'unsafe-inline' keyword, a hash ('sha256-R1WSspSmoxU9rGDX08R+aaprTREEUWr3i6Odp2Y1Szg='), or a nonce ('nonce-...') is required to enable inline execution. Note also that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

all.i.do.is.win/:1 Refused to load the script 'http://all.i.do.is.win:9337/remote.js' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

all.i.do.is.win/:1 Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

all.i.do.is.win/:11 Refused to frame 'http://you.cannot.win:9337/compromised.html' because it violates the following Content Security Policy directive: "child-src 'self'".

all.i.do.is.win/:9 Refused to execute inline event handler because it violates the following Content Security Policy directive: "default-src 'none'". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution. Note also that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.
```
