## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.
This uses this CSP header: `Content-Security-Policy: default-src 'none'; frame-src 'self' http://you.cannot.win:9337 ; child-src 'self' http://you.cannot.win:9337 ; script-src 'self' ;`
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
* Blocked loaing script src remote.js via you.cannot.win domain because that remote domain was not included in script-src
* Successfully loaded the iframe.
* Blocked the inline script in the iframe because you.cannot.win was not listed in the script-src CSP directive for the parent window ("inline event handler")
* Blocked the script block in the iframe because you.cannot.win was not listed in the scriptsrc CSP directive for the parent window

## Results in various browsers

OSX/Google Chrome 52.0:
```
all.i.do.is.win/:3 Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ". Either the 'unsafe-inline' keyword, a hash ('sha256-R1WSspSmoxU9rGDX08R+aaprTREEUWr3i6Odp2Y1Szg='), or a nonce ('nonce-...') is required to enable inline execution.

remote.js:1 This was written from remote.js on domain all.i.do.is.win
all.i.do.is.win/:1 Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' ".

compromised.html:3 Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ". Either the 'unsafe-inline' keyword, a hash ('sha256-On3coU+cyMfGNq2CE9q11EiGYPhN1xdMxoxlT5WV18c='), or a nonce ('nonce-...') is required to enable inline execution.

compromised.html:1 Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' ". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution.

all.i.do.is.win/:9 Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' ". Either the 'unsafe-inline' keyword, a hash ('sha256-...'), or a nonce ('nonce-...') is required to enable inline execution.

all.i.do.is.win/:1 Refused to load the image 'http://all.i.do.is.win:9337/favicon.ico' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'img-src' was not explicitly set, so 'default-src' is used as a fallback.
```
OSX/Safari 9.1.1:
```
[Error] Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ". (all.i.do.is.win, line 3)
[Error] Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' ".
[Log] This was written from remote.js on domain all.i.do.is.win (remote.js, line 1)
[Error] Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' ".
[Error] Unrecognized Content-Security-Policy directive 'child-src'. (x2)
[Error] Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ". (compromised.html, line 3)
[Error] Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' ". (compromised.html, line 1)
[Error] Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' ". (all.i.do.is.win, line 9)
```

OSX/Firefox 40.0:
```
Content Security Policy: Couldn't process unknown directive 'child-src'
Content Security Policy: The page's settings blocked the loading of a resource at self ("script-src http://all.i.do.is.win:9337").


console.log("PARENT: This w...

all.i.d...in:9337 (line 3)
This was written from remote.js on domain all.i.do.is.win
remote.js (line 1)
Content Security Policy: The page's settings blocked the loading of a resource at http://you.cannot.win:9337/remote.js ("script-src http://all.i.do.is.win:9337").
Content Security Policy: The page's settings blocked the loading of a resource at self ("script-src http://all.i.do.is.win:9337").


onload attribute on UNKNOWN element

Content Security Policy: Couldn't process unknown directive 'child-src'
Content Security Policy: The page's settings blocked the loading of a resource at self ("script-src http://you.cannot.win:9337").


onload attribute on UNKNOWN element

Content Security Policy: The page's settings blocked the loading of a resource at self ("script-src http://you.cannot.win:9337").


console.log('IFRAME: (from SCRI...

compromised.html (line 3)
```
