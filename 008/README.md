## Description

Demo of compromised 3rd party web page loaded in sandboxed iframe.

This uses this CSP header: `Content-Security-Policy: default-src 'none'; frame-src 'self' http://you.cannot.win:9337 ; child-src 'self' http://you.cannot.win:9337 ; script-src 'self' 'unsafe-inline';`
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

Note that the iframe was able to execute inline scripts despite not being allowed by parent window's script-src CSP header; the iframe sandbox's 'allow-scripts' setting overrides the script-src directive if the frame is permitted to load. (Allowing frames in CSP can be quite dangerous!)

* Executed the script block in the parent window because 'unsafe-inline' was included in script-src (index.html:4)
* Executed the inline script in the parent window because 'unsafe-inline' was not included in script-src (index.html:9)
* Executed the javascript in remote.js via the all.i.do.is.win domain because 'self' was included in script-src
* Blocked loaing script src remote.js via you.cannot.win domain because that remote domain was not included in script-src
* Successfully loaded the iframe.
* Blocked the inline script in the iframe because you.cannot.win was not listed in the script-src CSP directive for the parent window ("inline event handler")
* Blocked the script block in the iframe because you.cannot.win was not listed in the scriptsrc CSP directive for the parent window

## Results in various browsers

OSX/Google Chrome 52.0:
```
(index):4 PARENT: This was executed as part of a SCRIPT tag in the parent window.
remote.js:1 This was written from remote.js on domain all.i.do.is.win
(index):1 Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' 'unsafe-inline'".

compromised.html:4 IFRAME: (from SCRIPT block) xss within the iframe is possible, but restricted. A security error is raised when I try to access window.top.document:
compromised.html:5 Uncaught SecurityError: Sandbox access violation: Blocked a frame at "http://you.cannot.win:9337" from accessing a frame at "http://all.i.do.is.win:9337".  The frame requesting access is sandboxed and lacks the "allow-same-origin" flag.(anonymous function) @ compromised.html:5
compromised.html:1 IFRAME: This is an inline script executed when the compromised iframe was loaded.
(index):9 PARENT: This was executed as an inline script from the parent window.
(index):1 Refused to load the image 'http://all.i.do.is.win:9337/favicon.ico' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'img-src' was not explicitly set, so 'default-src' is used as a fallback.
```
OSX/Safari 9.1.1:
```
[Log] PARENT: This was executed as part of a SCRIPT tag in the parent window. (all.i.do.is.win, line 4)
[Error] Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' 'unsafe-inline'".
[Log] This was written from remote.js on domain all.i.do.is.win (remote.js, line 1)
[Error] Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "script-src 'self' 'unsafe-inline'".
[Error] Unrecognized Content-Security-Policy directive 'child-src'. (x2)
[Error] Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' ". (compromised.html, line 3)
[Error] Refused to execute inline event handler because it violates the following Content Security Policy directive: "script-src 'self' ". (compromised.html, line 1)
[Log] PARENT: This was executed as an inline script from the parent window. (all.i.do.is.win, line 9)
```

OSX/Firefox 40.0:
```
Content Security Policy: Couldn't process unknown directive 'child-src'

PARENT: This was executed as part of a SCRIPT tag in the parent window.     all.i.d...in:9337 (line 4)

This was written from remote.js on domain all.i.do.is.win                   remote.js (line 1)

Content Security Policy: The page's settings blocked the loading of a resource at http://you.cannot.win:9337/remote.js ("script-src http://all.i.do.is.win:9337 'unsafe-inline'").

Content Security Policy: Couldn't process unknown directive 'child-src'

PARENT: This was executed as an inline script from the parent window.       all.i.d...in:9337 (line 1)

Content Security Policy: The page's settings blocked the loading of a resource at self ("script-src http://you.cannot.win:9337").
    onload attribute on UNKNOWN element

Content Security Policy: The page's settings blocked the loading of a resource at self ("script-src http://you.cannot.win:9337").
    console.log('IFRAME: (from SCRI...                                      compromised.html (line 3)
```
