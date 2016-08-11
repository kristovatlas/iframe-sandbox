## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.
This uses this CSP header: `Content-Security-Policy: default-src 'none'; frame-src 'self' http://you.cannot.win:9337 ; child-src 'self' http://you.cannot.win:9337 ; script-src 'self' 'unsafe-inline' http://you.cannot.win:9337 ;`
This uses this sandbox attribute: `sandbox="allow-scripts allow-same-origin"`

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
* Blocked the iframe from accessing window.top.document because the iframe is a cross-domain iframe. Note that in the previous version of this test, the error message claimed that the problem was with the lack of "allow-same-origin" but even with this set, it's still cross-domain.

## Results in various browsers

OSX/Google Chrome 52.0:
```
(index):4 PARENT: This was executed as part of a SCRIPT tag in the parent window.
remote.js:1 This was written from remote.js on domain all.i.do.is.win
remote.js:1 This was written from remote.js on domain all.i.do.is.win
compromised.html:4 IFRAME: (from SCRIPT block) xss within the iframe is possible, but restricted. A security error is raised when I try to access window.top.document:
compromised.html:5 Uncaught SecurityError: Blocked a frame with origin "http://you.cannot.win:9337" from accessing a frame with origin "http://all.i.do.is.win:9337". Protocols, domains, and ports must match.(anonymous function) @ compromised.html:5
remote.js:1 This was written from remote.js on domain you.cannot.win
compromised.html:1 IFRAME: This is an inline script executed when the compromised iframe was loaded.
(index):9 PARENT: This was executed as an inline script from the parent window.
```

OSX/Firefox 40.0:
```
Content Security Policy: Couldn't process unknown directive 'child-src'
PARENT: This was executed as part of a SCRIPT tag in the parent window.                                                                                 all.i.d...in:9337 (line 4)
This was written from remote.js on domain all.i.do.is.win                                                                                               remote.js (line 1)
This was written from remote.js on domain all.i.do.is.win                                                                                               remote.js (line 1)
Content Security Policy: Couldn't process unknown directive 'child-src'
IFRAME: (from SCRIPT block) xss within the iframe is possible, but restricted. A security error is raised when I try to access window.top.document:     compromised.html (line 4)
Error: Permission denied to access property "document"
    console.log(window.top.document);                                                                                                                   compromised.html (line 5, col 8)
This was written from remote.js on domain you.cannot.win                                                                                                remote.js (line 1)
IFRAME: This is an inline script executed when the compromised iframe was loaded.                                                                       compromised.html (line 1)
PARENT: This was executed as an inline script from the parent window.                                                                                   all.i.d...in:9337 (line 1)
```

OSX/Safari 9.1.1:
```
[Log] PARENT: This was executed as part of a SCRIPT tag in the parent window. (all.i.do.is.win, line 4)
[Log] This was written from remote.js on domain all.i.do.is.win (remote.js, line 1)
[Log] This was written from remote.js on domain all.i.do.is.win (remote.js, line 1)
[Error] Unrecognized Content-Security-Policy directive 'child-src'. (x2)

[Log] IFRAME: (from SCRIPT block) xss within the iframe is possible, but restricted. A security error is raised when I try to access window.top.document: (compromised.html, line 4)
[Error] Blocked a frame with origin "http://you.cannot.win:9337" from accessing a frame with origin "http://all.i.do.is.win:9337". Protocols, domains, and ports must match.
	global code (compromised.html:5)
[Log] undefined (compromised.html, line 5)
[Log] This was written from remote.js on domain you.cannot.win (remote.js, line 1)
[Log] IFRAME: This is an inline script executed when the compromised iframe was loaded. (compromised.html, line 1)
[Log] PARENT: This was executed as an inline script from the parent window. (all.i.do.is.win, line 9)
```
