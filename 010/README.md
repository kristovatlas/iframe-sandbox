## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.
This uses this CSP header: `Content-Security-Policy: default-src 'none'; child-src 'self' http://you.cannot.win:9337 ; script-src 'self' 'unsafe-inline' http://you.cannot.win:9337 ;`
This uses this sandbox attribute: `sandbox="allow-scripts"`

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

* Executed the script block in the parent window because 'unsafe-inline' was included in script-src (all.i.do.is.win/:3)
* Executed the inline script in the parent window because 'unsafe-inline' was included in script-src (all.i.do.is.win/:9)
* Executed the javascript in remote.js via the all.i.do.is.win domain because 'self' was included in script-src
* Executed the javascript in remote.js that was loaded in the parent window from you.cannot.win because the domain was included in the script-src
* Executed the javascript in remote.js that was loaded in the iframe and hosted on you.cannot.win because the domain was included in the script-src and the iframe had the "allow-scripts" sandbox permission
* Successfully loaded the iframe.
* Executed the inline script in the iframe because the 'allow-scripts' permission was included in the iframe sandbox attribute ("inline event handler")
* Executed the script block in the iframe because 'allow-scripts' permission was not included in the iframe sandbox attribute
* Blocked the iframe from accessing window.top.document because the iframe is a cross-domain iframe.

## Results in various browsers

OSX/Google Chrome 52.0:
```
(index):4 PARENT: This was executed as part of a SCRIPT tag in the parent window.
remote.js:1 This was written from remote.js on domain all.i.do.is.win
remote.js:1 This was written from remote.js on domain all.i.do.is.win
compromised.html:4 IFRAME: (from SCRIPT block) xss within the iframe is possible, but restricted. A security error is raised when I try to access window.top.document:
compromised.html:5 Uncaught SecurityError: Sandbox access violation: Blocked a frame at "http://you.cannot.win:9337" from accessing a frame at "http://all.i.do.is.win:9337".  The frame requesting access is sandboxed and lacks the "allow-same-origin" flag.(anonymous function) @ compromised.html:5
remote.js:1 This was written from remote.js on domain you.cannot.win
compromised.html:1 IFRAME: This is an inline script executed when the compromised iframe was loaded.
(index):9 PARENT: This was executed as an inline script from the parent window.
```
