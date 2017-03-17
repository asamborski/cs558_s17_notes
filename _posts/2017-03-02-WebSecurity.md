---
layout: notes  
title: Web Security   
scribe: Dor Rubin
---



## Presentation: WhatsApp Public Key

 * A UC Berkeley Student found a vulnerability that allows *anyone* to impersonate recipients of a message. 
   * Note: anyone is italicized because one is required access to a piece of technology that is difficult to obtain 
 * That machine is a SS7 Hub which allows an adversary to impersonate a GSM cell tower and record information such as phone number and IMEI
 * Using that information and while the target recipient is offline, the adversary can redirect messages to an alternate device
 * Has forward secrecy because old messages will not be seen, however future communication is easily deciphered. 
 * Whether this exposed a vulnerability or backdoor is hotly debated. Still this bring up an important **tradeoff between secrecy and usability** with product design



## Web Security

### HTTP vs. HTTPS

 * HTTPS: browser communication uses TLS to encrypt communication
  * the S represents the layers of key exchange and encryption & authentication that sit on top of HTTP
 * HTTP sent over TCP
 
### Threat models
* Man in the Middle (MITM)
  * Double sided arrows from Alice to Eve and from Eve to Bob
  * Eve can see everything between A, B. (ex. send, add, drop, modify, receive communication)
 ```
  Alice <--> Eve <--> Bob
``` 
 * Remote Attacker (Off-path)
  * Double sided arrows from Alice to Bob and unidirectional arrows to Alice and Bob from Eve
  * Eve can not see anything between the browser and server. Only send messages to each like a normal computer
```
  Alice <--> Bob  
  ^-- Eve --^
``` 

  * On Path Attacker
    * Double sided arrows from Alice to Bob and that is tapped by Eve. Unidirectional arrows to Alice and Bob from Eve               
  * Attacker can see anything between the browser and server but not able to modify or drop traffic from Alice to Bob. 
```
    Alice <--.--> Bob
     ^-----|-----^  
                Eve
``` 

   * Summary from easiest to hardest attack to do once have access
     1. MITM -- Most access to information, easiest to do, Most powerful
     2. On Path 
     3. Off-Path -- Least access, hardest to do, Least powerful  
    **If you were a MITM you could also do a 'lesser' attack**
    
### HTTP

ex. http://bu.edu:81/class?name=cs558#homework  

| Protocol  | Domain | Port | Path | Query | Fragment |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| http  | bu.edu  | 81  | class  | ?name=cs558  | #homework |


HTTP Request Components:
1. Method ex. GET, POST
2. File
3. HTTP version
4. **User-agent**   
5. **Referrer**
Exposes user information

HTTP Responses Components:

| 1xx  | 2xx  | 3xx  | 4xx  | 5xx  |
| ---------| --------- |-------| -----|-----|
| Informational | Success   | Redirect     | Client Error    | Server Error|

ex. 404 - Page not found!

### HTML and DOM
Example of tags:
```html
<p></p> <!–select with '<element>' –>  
<a class=""></a>  <!–select with .<class_name> –>  
<img id=""> <!–select with #<class_name> –>
```

 * Security issues
  * embedding images: if from external server can include single pixel tracking
  * allows second server to view the same information as the first



### Cookies
In the past, servers did not know anything about you from a single request... it was stateless
```
        POST
Client ------------> Server
        Cookie(w/state)
Client <------------ Server

    .... user goes to another page
        
        Cookie
Client ------------> Server
        state
Client <------------ Server        

```
Now services can use cookies can look up information about a user

Cookie Ingredients:
1. domain (domain specific-- ie. Facebook doesn't use Google's cookies)
2. expiration, security settings, SSL

Cookie Authentication
1. Browser --- POST, login username, pw -----> Web Server
2. Web Server  --- Validate -----> Auth Server
3. Auth Server --- auth = val -----> Web Server
4. Web Server --- set -cookies, auth = val  -----> Browser
5. Browser --- GET restricted.html, cookie: auth= val -----> Web Server
6. Web Server  --- restricted.html, auth = val -----> Auth Server
7. Auth Server --- YES/NO -----> Web Server
8. Web Server --- if YES, restricted.html  -----> Browser

**Security = Browser only sends cookies over HTTPS** 



### Cross-site Scripting
JavaScript can modify DOM, set cookies, etc
ex. visit hackers.com what prevents it from reading bank.com cookies  
  ---> Same Origin Policy (SOP), hackers.com can only access cookies from hackers.com

SOP: if A.com has image that includes script from somewhere else, that script should not be able to have access to page elements

Stored XSS: 
1. inject malware script
2. user makes request
3. user receives malware script
4. performs attack
