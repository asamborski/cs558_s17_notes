---
layout: notes
title: "Introduction to Web Security"
scribe: Luke Osborne
---

# Table of Contents
- [News Presentation](#news-presentation)
- [Lecture Notes](#lecture-notes)
  * [Webpage Security](#webpage-security)
  * [Threat Models](#threat-models)
  * [URLs](#urls)
  * [HTTP](#http)
  * [Cookies](#cookies)



# News Presentation
## 2016 WhatsApp Public Key Vulnerability

### Overview
----
  * There was a vulnerability revealed which made it possible for someone to impersonate another person and receive "secure" messages from a WhatsApp conversation.
  * If a user buys and registers a GSM SS7 Hub (which is possible in some countries, but not the US), it is possible to register using someone else's phone number and other information.
  * Then the person can bypass WhatsApps authentication, which used a text message verification.
  * Any pending messages will be sent to the attacker, and the attacker can read any future messages, but previous messages are still safe (forward secrecy is preserved).
  * WhatsApp generates a new key for the attacker to use, which is different than the old one.

### Takeaways
----
  * WhatsApp is aware of this vulnerability, and has made some changes, but it mainly becomes a case of usability vs. security.
  * WhatsApp could notify users by default every time the person they are talking to re-generates a key, but chooses to not have this option set as defualt.
  * Pending messages could be not sent by default (the app Signal does this), but again, WhatsApp sends them automatically, even if the user chooses to be notified.


# Lecture Notes
## Webpage Security
### Protocols
----
  * **TLS** - Encrypted Protocol
  * **HTTP**- Webpage protocol (actual messages being passed)

```
  +--------------+
  | Key Exchange |
  +--------------+
  Encrypted & Authenticated Channel
  ______________________

    [http]-->
  ______________________


        Uses 
HTTP 	<--> 	TCP
HTTPS 	<--> 	TLS
```

### What can a MITM (Man-in-the-Middle) do to each?
----
  * For HTTP, a MITM can see, read, and modify what is sent.
  * For HTTPS, a MITM can only see *encrypted* messages, but cannot read or modify.


## Threat Models
### MITM (Man-in-the-Middle)
----
  * Attacker can send, eavesdrop, and modify
  * Model assumed by cryptographers

```
  +----------+                    +----------+
  |          |-------+------+---->|          |
  |  Alice   |       | MITM |     |   Bob    |
  |          |<------+------+---- |          |
  +----------+                    +----------+
```

### On-Path
----
  * Attacker can eavesdrop, but not modify traffic or drop traffic.
  * For example, someone sniffing on a network.
  * Attacker can send separate messages to Alice or Bob from different box

```
  +----------+                     +----------+
  |          |-------------------->|          |
  |  Alice   |                     |   Bob    |
  |          |<------------------- |          |
  +----------+         .           +----------+
    ^ |                .                  | ^ 
    | |                .                  | |
    | |           +-----------+           | |     
    | |           | Attacker  |           | |     
    | |           | Eavesdrop |           | |     
    | |           +-----------+           | |      
    | |                                   | |
    | |           +----------+            | |
    | |__________>| Attacker |<___________| |
    |_____________|   Box    |______________|                 
                  +----------+
```

### Off-Path
----
  * Model assumed by web security
  * No eavesdropping capability
  * Only able to send messages

```
  +----------+                     +----------+
  |          |-------------------->|          |
  |  Alice   |                     |   Bob    |
  |          |<------------------- |          |
  +----------+                     +----------+
    ^ |                                   | ^ 
    | |                                   | |      
    | |                                   | |
    | |           +----------+            | |
    | |__________>| Remote   |<___________| |
    |_____________| Attacker |______________|                 
                  +----------+
```

## URLs
----
  * Global identifiers of network-retrievable documents

```
  URL: http://bu.edu:81/class?name=cs558#homework

  +--Protocol--+--hostname--+-port-+--path---+--query-----+--fragment--+
  |   http://  |   bu.edu   | :81  | /class? | name=cs558 | #homework  |
  +--------------------------------------------------------------------+
```

  * Special characters are encoded as hex

## HTTP
### HTTP Requests
----
#### How to see (Firefox):
  * Right-click -> inspect element -> network -> reload -> choose packet -> headers -> Raw Headers -> Request Headers

```
GET /index.html HTTP/1.1
Accept: image/gif, image/x-bitmap, image/jpeg, */*
Accept-Language: en
Connection: Keep-Alive
User-Agent: Mozilla/1.22 (compatible; MSIE 2.0; Windows 95)
Host: www.example.com
Referer: http://www.google.com?q=dingbats
(BLANK LINE)
(DATA, NONE FOR GET)
```

```
+==============+=============+
| Method       |  GET        |
+==============+=============+
| File         | index.html  |
+--------------+-------------+
| HTTP Version | 1.1         |
+--------------+-------------+
| Headers      | */*         |
+--------------+-------------+
```

  * GET: No side effect
  * POST: Possible side effect

### HTTP Responses
----
#### How to see (Firefox):
  * Right-click -> inspect element -> network -> reload -> choose packet -> headers -> Raw Headers -> Response Headers

```
Host: www.cs.bu.edu
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:51.0) Gecko/20100101 Firefox/51.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Cookie: [REDACTED]
Connection: keep-alive
Upgrade-Insecure-Requests: 1
If-Modified-Since: Tue, 20 Dec 2016 19:15:41 GMT
If-None-Match: [REDACTED]
Cache-Control: max-age=0
```

### Old Style HTML vs New Style HTML
#### Old Style HTML:
----
  * Mostly text markup

```html
<html>
  <body>
  <ul id="t1">
    <li> Item1 </li>
  </ul>  
  </body>
</html>
```

#### New Style HTML:
----
  * More dynamic
  * Uses JavaScript to execute code in browser

```javascript
var list = document.getElementByID("t1");
var rewrite = document.createElement("1");
...
```

### HTML Image Tags and Vulnerabilities
----
An image tag in an HTML page, like:

```html
<img src = "http://notthesamewebsite.com/information_can_be_passed_here">
```

can be used to force a user to visit another web server when loading a page.

This can be used to track users (single pixel images), or pass information, for example in an XSS (cross-site-scripting) attack (covered later).

## Cookies
  * Cookies are used to store a state on the user's machine.
  * They are needed because servers are generally stateless.

```

  +---------+     POST                  +---------+
  |         |-------------------------->|         |
  | Browser |                           | Server  |
  |         |<--------------------------|         |
  +---------+     HTTP Header           +---------+
      Set-cookie: NAME=VALUE
            domain=(who can read);
            expires=(when expires);
            secure=(only use SSL);
```

  * Once the cookie has been sent to the browser, it can provide it back to the server to restore the previous state. 

```

  +---------+     POST                  +---------+
  |         |-------------------------->|         |
  | Browser |    COOKIE: Name=VALUE     | Server  |
  |         |                           |         |
  +---------+                           +---------+

```

### Cookie Authentication
----

```

  +---------+ POST login.cgi       +---------+   Validate user      +---------+
  | Browser |--------------------->|  Web    |--------------------->|  Auth   |
  |         | username&pwd         | Server  |                      | Server  |
  |         |                      |         |                      |         |
  |         | Set cookie: auth=val |         |    auth=val          |         |
  |         |<---------------------|         |<-------------------- |         |
  |         |                      |         |                      |         |
  |         |                      |         |                      |         |
  |         | GET restricted.html  |         |    restricted.html   |         |
  |         |--------------------->|         |--------------------> |         |
  |         |  cookie: auth=val    |         |    auth=val          |         |
  |         |                      |         |                      |         |
  |         |                      |         |                      |         |
  |         | if YES,              |         |                      |         |
  |         |<---------------------|         |<-------------------- |         |
  |         | restricted.html      |         |       YES/NO         |         |
  +---------+                      +---------+                      +---------+
```

### Secure Cookies
----
  * Browser will only seend cookie back over HTTPS

### How to see cookies in browser (Firefox):
----
  1. Download the "Web Developer Toolbar" Firefox extension
  2. In the toolbar, select "Cookies", then "View Cookie Information"

### How to see cookies in browser (Chrome):
----
  1. Click the menu "skittles" (3 dots) in top right corner.
  2. Click "More Tools"
  3. Click "Developer Tools"
  4. Click the "Application" tab
  5. See "Cookies" under "Storage" section

### httpOnly Cookies
----
  * Setting for using cookies over HTTP(S), but not allowing them to be accessible to scripts
  * Cookies cannot be accessed via `document.cookie`
  * Helps to prevent XSS (Cross-Site Scripting--future topic)

### SOP (Same Origin Policy)
----
  * Only pages with the same hostname should be able to access that cookie
  * Exceptions: Embedded scripts from other hostnames CAN execute and access cookies.
  * So if, through XSS, someone embeds a javascript tag, the cookies can be stolen

