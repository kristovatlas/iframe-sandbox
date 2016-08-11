## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.
This uses this CSP header: `Content-Security-Policy: default-src 'none'; child-src 'self' http://you.cannot.win:9337 ; script-src 'self' ;`
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

* Blocked the script block in the parent window because 'unsafe-inline' was not included in script-src (all.i.do.is.win/:3)
* Blocked the inline script in the parent window because 'unsafe-inline' was not included in script-src (all.i.do.is.win/:9)
* Executed the javascript in remote.js via the all.i.do.is.win domain because 'self' was included in script-src
* Blocked remote.js via you.cannot.win because that remote domain was not included in script-src
* Successfully loaded the iframe.
* Blocked the inline script in the iframe because the 'allow-scripts' permission was not included in the iframe sandbox attribute ("inline event handler")
* Blocked the script block in the iframe because 'allow-scripts' permission was not included in the iframe sandbox attribute

## Results in various browsers

OSX/Google Chrome 52.0:
```
Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ". Either the 'unsafe-inline' keyword, a hash ('sha256-R1WSspSmoxU9rGDX08R+aaprTREEUWr3i6Odp2Y1Szg='), or a nonce ('nonce-...') is required to enable inline execution. all.i.do.is.win/:3

remote.js:1 This was written from remote.js on domain all.i.do.is.win
all.i.do.is.win/:1 Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' ".

compromised.html:1 Blocked script execution in 'http://you.cannot.win:9337/compromised.html' because the document's frame is sandboxed and the 'allow-scripts' permission is not set.
compromised.html:1 Blocked script execution in 'http://you.cannot.win:9337/compromised.html' because the document's frame is sandboxed and the 'allow-scripts' permission is not set.
all.i.do.is.win/:9 Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' ". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution.
```
