## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.
This uses this CSP header: `Content-Security-Policy: default-src 'none'; child-src 'self' http://you.cannot.win:9337 ; script-src 'self' http://you.cannot.win:9337 ;`
This uses this sandbox attribute: `sandbox="allow-scripts"`

## Prerequisites

Add to `/etc/hosts` file:
```
127.0.0.1       all.i.do.is.win
127.0.0.1       you.cannot.win
```

## Usage

`python server.py`

In your browser, visit: http://all.i.do.is.win:9337/

## Summary of results

* Blocked the script block in the parent window because 'unsafe-inline' was not included in script-src (all.i.do.is.win/:3)
* Blocked the inline script in the parent window because 'unsafe-inline' was not included in script-src (all.i.do.is.win/:9)
* Executed the javascript in remote.js via the all.i.do.is.win domain because 'self' was included in script-src
* Executed the javascript in remote.js that was loaded in the parent window from you.cannot.win because the domain was included in the script-src
* Executed the javascript in remote.js that was loaded in the iframe and hosted on you.cannot.win because the domain was included in the script-src and the iframe had the "allow-scripts" sandbox permission
* Successfully loaded the iframe.
* Blocked the inline script in the iframe because the 'allow-scripts' permission was not included in the iframe sandbox attribute ("inline event handler")
* Blocked the script block in the iframe because 'allow-scripts' permission was not included in the iframe sandbox attribute

## Results in various browsers

OSX/Google Chrome 52.0:
```
all.i.do.is.win/:3 Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' http://you.cannot.win:9337 ". Either the 'unsafe-inline' keyword, a hash ('sha256-R1WSspSmoxU9rGDX08R+aaprTREEUWr3i6Odp2Y1Szg='), or a nonce ('nonce-...') is required to enable inline execution.

remote.js:1 This was written from remote.js on domain all.i.do.is.win
remote.js:1 This was written from remote.js on domain all.i.do.is.win
compromised.html:3 Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' http://you.cannot.win:9337 ". Either the 'unsafe-inline' keyword, a hash ('sha256-On3coU+cyMfGNq2CE9q11EiGYPhN1xdMxoxlT5WV18c='), or a nonce ('nonce-...') is required to enable inline execution.

remote.js:1 This was written from remote.js on domain you.cannot.win
compromised.html:1 Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' http://you.cannot.win:9337 ". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution.

all.i.do.is.win/:9 Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' http://you.cannot.win:9337 ". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution.
```
