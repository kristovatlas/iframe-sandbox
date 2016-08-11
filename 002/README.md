## Description

Demo of compromised 3rd party web page loaded in non-sandboxed iframe. This uses this CSP header: `Content-Security-Policy: default-src 'none';`

## Prerequisites

Add to `/etc/hosts` file:
```
127.0.0.1       all.i.do.is.win
127.0.0.1       got.money.on.my.mind
```

## Usage

`python server.py`

In your browser, visit: http://all.i.do.is.win:9337/

## Results in various browsers

OSX/Google Chrome 52.0:

OSX/Firefox 40.0.3:
```
Content Security Policy: The page's settings blocked the loading of a resource at self ("default-src 'none'").


console.log("PARENT: This w...

all.i.d...in:9337 (line 3)
Content Security Policy: The page's settings blocked the loading of a resource at http://all.i.do.is.win:9337/remote.js ("default-src 'none'").
Content Security Policy: The page's settings blocked the loading of a resource at http://you.cannot.win:9337/remote.js ("default-src 'none'").
Content Security Policy: The page's settings blocked the loading of a resource at self ("default-src 'none'").


onload attribute on UNKNOWN element

Content Security Policy: The page's settings blocked the loading of a resource at http://you.cannot.win:9337/compromised.html ("default-src 'none'").
```

OSX/Firefox 47:
TODO

OSX/Safari 9.1.1:
```
[Error] Refused to execute inline script because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback. (all.i.do.is.win, line 3)

[Error] Refused to load the script 'http://all.i.do.is.win:9337/remote.js' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

[Error] Refused to load the script 'http://you.cannot.win:9337/remote.js' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback.

[Error] Refused to load frame 'http://you.cannot.win:9337/compromised.html' because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'frame-src' was not explicitly set, so 'default-src' is used as a fallback.

[Error] Refused to execute inline event handler because it violates the following Content Security Policy directive: "default-src 'none'". Note that 'script-src' was not explicitly set, so 'default-src' is used as a fallback. (all.i.do.is.win, line 9)
```
