## Description

Demo of compromised 3rd party web page loaded in non-sandboxed iframe

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
PARENT: This was executed as part of a SCRIPT tag in the parent window.
remote.js:1 This was written from remote.js on domain all.i.do.is.win
remote.js:1 This was written from remote.js on domain all.i.do.is.win
compromised.html:4 IFRAME: xss within the iframe is possible, but restricted. A security error is raised when I try to access window.top.document:
compromised.html:5 Uncaught SecurityError: Blocked a frame with origin "http://you.cannot.win:9337" from accessing a frame with origin "http://all.i.do.is.win:9337". Protocols, domains, and ports must match.(anonymous function) @ compromised.html:5
compromised.html:1 IFRAME: This is an inline script executed when the compromised iframe was loaded.
(index):9 PARENT: This was executed as an inline script from the parent window.
```
