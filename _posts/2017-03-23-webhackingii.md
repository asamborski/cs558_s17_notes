---
layout: notes
title: web hacking ii 
scribe: Molly Shopper
---

# web hacking ii: cookies, SOP, XSS, and CSRF

## Cookies
* EditThisCookie on Chrome
* What are cookies?
  * key value pairs stored in the browser, store session information
  * make get request to website
  * key=value pair (AKA Cookie Crumb)
	* can set the domain: who can access the cookie?
    - bar.foo.com - yes
    - *.com - no
    - foo.com/bar - yes
    - is .foo.com different from foo.com? no
  * path: what pages should the cookie be sent with?
  * flags:
    - HostOnly: can only be read by the same domain that set it, foo.example.com sets it so bar.example.com cannot read it
    - Session: a session cookie expires when te user navigates away from the website
    - Secure: cookie can only be sent over https
    - HttpOnly: cannot be accessed with JavaScript (prevents a lot of XSS)
  * zombie cookies
    - cookie that is automatically recreated after being deleted
    - accomplished by storing the cookies content in multiple locations, such as Flash Local shared object, HTML5 web storage
  * 3rd party cookies (3P)
    - used for ad tracking
    - most browsers (firefox, IE, opera, Google chrome) allow third party cookies by default, as long as website has compact privacy policy published. new version of safari blocks
    - go to huffingtonpost.com- inspect -> application -> cookies- only one is huffingtonpost, others have at least 10 cookies. how to include 3rd party cookie on page? iframe = page within a page, make get request to external domain. javascript uses zombie cookie, sets local storage to have the same value as cookie, when reset, brings back the cookie- cant delete them permanently
    - used for ad selling
  * in the news
    - EU has new law (2012) that requires agree to have cookies stored on their page, for non essential purposes- largely ineffective and annoying

  * dynamic web pages
    - rather than static HTML, web pages can be expressed as a program, say written in JavaScript
  * Javascript
    - embedded in web pages
    - scripts are executed by browser
  * confining the power of javascript scripts
    - browsers need to mae sure JS scripts dont abuse it
## Same Origin Policy (SOP)
  - only scripts received from a web  page's origin have access to page's elements
  - for http://www.example.com:
    - http://www.example.com/dir/page2.html - yes
    - http://v2.www.example.com/dir/other.html - no
    - http://username:password@www.example.com/dir2/other.html - yes
    - http://www.example.com:81/dir/other.html - no
    - https://www.example.com/dir/other.html - no
    - http://en.example.com.dir/other.html - no
    - http://example.com/dir/other.html - no
    - http://www.example.com:80/dir/other.html - maybe
      
  - are subdomains included in SOP? yes
  - what about local storage? should example.com be able to set local storage on a.example.com? yes
    - www.filldisk.com - filling local storage with pictures of cats, 5 mb per subdomain
## XSS: subverting the same origin policy
  - it would be bad if attacker from evil.com can fool your browser into attacking script of their choice with your browser believing the scripts origin to be some other site, like bank.com
  - one nasty/general apprach for doing so is trick the server of interest to send the attacker's script to browser (XSS)
  - Two types of XSS:
    - stored/persistent: attacker leaves their script lying around on server
      - Target: user with Javascript-enabled browser who visits user-generated-content page on vulnerable web service
      - Attacker goal: run script in user’s browser with same access as provided to server’s regular scripts (subvert SOP)
      - Attacker tools: ability to leave content on web server page; optionally, a server used to receive stolen information such as cookies
      - Key trick: server fails to ensure that content uploaded to page does not contain embedded scripts
    - reflected: doing in lab; the attacker gets you to send the bank.com server a URL with a Javascript script and the server echoes it back to you in its response 
      - only targeting people on site, with link- more effective
      - Target: user with Javascript-enabled browser who visits a vulnerable web service that will include parts of URLs it receives in the web page output it generates
      - Attacker goal: run script in user’s browser with same access as provided to server’s regular scripts (subvert SOP)
      - Attacker tools: ability to get user to click on a speciallycrafted URL; optionally, a server used to receive stolen information such as cookies
      - Key trick: server fails to ensure that output it generates does not contain embedded scripts other than its own
  - protecting servers against XSS (OWASP)
    - OWASP = open web application security project
    - best way to protect against xss attacks:
      - white listing: ensure that your app validates all headers, cookies, quiery strings, form fields, and hidden fields (all parameters) against rigorous specifications
    - web accesses with side effects
      - what happens if we visit evilsite.com which includes an image that transfers money?
## Cross-site Request Forgery (CSRF)
  - Target: user who has some sort of account on a vulnerableserver where requests from the user’s browser to the server have a predictable structure
  - Attacker goal: make requests to the server via the user’s browser that look to server like user intended to make them
  - Attacker tools: ability to get user to visit a web page under the attacker’s control
  - Key tricks: (1) requests to web server have predictable structure; (2) use of <IMG5SRC=…> or such to force victim’s browser to issue such a (predictable) request

  * boomerang
    - track emails, shows when user opens email
      - there is an image in the content, automatically rendered, display:none so image does not show
        - browser makes a get request to url, sends id back to server, url is proxied through google to maintain same origin


