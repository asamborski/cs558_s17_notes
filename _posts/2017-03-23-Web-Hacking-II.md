---
layout: notes
title: Web Hacking II - Cookies, SOP, XSS and CSRF
scribe: Jennifer Tsui
---

### Tools
- [EditThisCookie](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=en)

### What's the point of using cookies?
- To track users and to keep them logged in
- To remember what's in your shopping cart
- To make websites stateful
  
### Setting Cookies
- Key=value pair: aka cookie crumb
- Expiration date: if not set, cookie is Session cookie
- Domain: allowed to access cookie
  - who can access?
    - bar.foo.com - Yes
    - *.com - No
    - foo.com/bar - Yes
  - is .foo.com different from foo.com? NO!!
- What pages should the cookie set with? path
- Flags
  - **HostOnly:** Can only be read by same domain that set it, foo.example.com sets it so bar.example.com cannot read it if HostOnly=1
  - **Session:** A session cookie expires when the user navigates away from the website
  - **Secure:** Cookie can only be sent over https
  - **HTTPOnly:** Cannot be accessed with JavaScript (prevents most XSS)
  
```  
          +-----------+                                                       +-----------+
          |           |                                                       |           |
          |           |     POST \login                                       |           |
          |           |     Host: cs558web.bu.edu                             |           |
          |           |     username=attacker&password=l33th4x                |           |
          |           |  ------------------------------------------------->>  |           |
          |           |                                                       |           |
          |           |     HTTP/1.1 303 See Other                            |           |
          |           |     Location: http://cs558web.bu.edu/project2/        |           |
          |           |     Set-Cookie: authuser="!28734y8273"                |           |
          |   USER    |  <<-------------------------------------------------  | WEBSERVER |
          |           |                                                       |           |
          |           |     GET /project2/ HTTP/1.1                           |           |
          |           |     Host: cs558web.bu.edu                             |           |
          |           |     Cookie: authuser="!28734y8273";                   |           |
          |           |  ------------------------------------------------->>  |           |
          |           |                                                       |           |
          |           |    HTTP/1.1 200 OK                                    |           |
          |           |                                                       |           |
          |           |    <html>...Protected HTML...</html>                  |           |
          |           |  <<-------------------------------------------------  |           |
          +-----------+                                                       +-----------+
```
  
### Zombie Cookies
 - cookie that's automatically recreated after being deleted.
 - How is this done?
    - by storing the cookie's content in mult locations, such as Flash local shared object, HTML5 Web storage, and other client-side and even server-side locations. 
    - When the cookie's absence is detected, the cookie is recreated using the data stored in these locations.           
 
### 3rd Party Cookies (3P)
- Used for ad tracking
- Firefox, Internet Explorer, Opera, and Google Chrome do **allow third-party cookies by default**, as long as the tird party website has Compact Privacy Policy published.
    - Newer versions of Safari block third-party cookies.
- To check for them...
    - Right click, then click 'Inspect' in Google Chrome
    - For Safari, [do this](https://support.apple.com/kb/PH21414?viewlocale=en_LA&locale=en_LA)
- How do these cookies get included?
    - Have iframe, and look what's included in that page. See that it has some javascript.
    - Take a look at local storage; all of these tracking sites have some local storage files stored. 
    - cookies can track you across different websites
    - Companies say: 'Hey, put this script in the page. Based on the number of views, we will give you $$$'
  
```
<iframe name="fb_xdm_frame_http" frameborder="0" allowtransparency="true" allowfullscreen="true" scrolling="no" id="fb_xdm_frame_http" aria-hidden="true" title="Facebook Cross Domain Communication Frame" tabindex="-1" src="http://staticxx.facebook.com/connect/xd_arbiter/r/1FegrZjPbq3.js?version=42#channel=fa383a9b0dbf38&amp;origin=http%3A%2F%2Fwww.huffingtonpost.com" style="border: none;"></iframe>
```
```
HTTP/1.0 200 OK
Set-Cookie: JEB2=58D...;expires=Sat, 23 Mar 2019 16:11:20 GMT; 
domain=atwola.com;path=/
```

### In the news
- EU requires users agree to have cookies tored on their page
- Let's try it!
  - Go to google.com
  - Change location to France (Sean used PIA)
  - See change at http://www.ipfingerprints.com/
  - Refresh google.com
  
### Dynamic web pages
- Rather than Static HTML, web pages can be expressed as a **program**, say written in *javascript*:

```
<title>Javascript demo page</title>

<font size=30>
Hello, <b>
<script>
var a = 1;
var b = 2;
document.write("world: ", a+b, "</b>");
</script>
```

### Javascript
- Web page programming language
- Scripts are embedded in web pages returned by web server
- Scripts are executed by browser

### Confining the Power of Javascript Scripts
- Browsers need to ensure that JS scripts don't abuse their power
    - don't want a script sent from **hackerz.com** web server to read cookies belonging to **bank.com**
    - ... or read keystrokes typed by user at **bank.com**

### Same Origin Policy (SOP)
- broswers provide isolations for JS scripts via the **Same Origin Policy** 
- Simple version:
    - broswer associates web page elements (layout, cookies, events) with a given *origin*
      - origin: web server that provided the page/cookies in the first place
      - Identity of web server is in terms of its hostname, e.g., **bank.com**
- SOP = **only scripts received from a web page’s origin have access to page’s elements**

- Origin Definition
  - http://www.example.com:80/dir/page/htm
    - **Protocol:** http://
    - **Host:** www.example.com
    - **Port:** :80
  - **http** operates on port *80* so http://example.com/ = http://example.com:80/
  - **https** operates on port *443* so https://example.com/ = https://example.com:443/	

- Should the following be able to access cookies from http://www.example.com? 

|                           Site                         | Yes/No/Maybe |
| ------------------------------------------------------ | ------------:|
http://www.example.com/dir/page2.html                    | Yes          |       
http://v2.www.example.com/dir/other.html                 | No           |
http://username:password@www.example.com/dir2/other.html | Yes          |
http://www.example.com:81/dir/other.html                 | No           |
https://www.example.com/dir/other.html                   | No           |
http://en.example.com/dir/other.html                     | No           |
http://example.com/dir/other.html                        | No           |
http://www.example.com:80/dir/other.html                 | Maybe        |

- Are subdomains included in SOP? YES
- What about local storage? Should **example.com** be able to set local storage on **a.example.com**?
- Implementation differs in the wild: http://www.filldisk.com/

### Cross-Site Scripting (XSS): Subverting the SOP
- It’d be Bad if an attacker from evil.com can fool your browser into executing script of their choice, with your browser believing the script’s origin to be some other site, like bank.com
- One general approach for doing this is tp trick the server of interest (e.g., bank.com) to actually send the attacker’s script to your browser
    - Then no matter how carefully your browser checks, it’ll view script as from the same origin (because it is!) and give it all that powerful/nasty access
    
### Two Types of XSS
- **Stored (or "persistant")**
    - *Target:* user with Javascript-enabled browser who visits user-generated-content page on vulnerable web service
    - *Attacker goal:* run script in user’s browser with same access as provided to server’s regular scripts (subvert SOP)
    - *Attacker tools:* ability to leave content on web server page (e.g., via an ordinary browser); optionally, a server used to receive stolen information such as cookies
    - *Key trick:* server fails to ensure that content uploaded to page does not contain embedded scripts
    - *Notes:* 
        (1) do not confuse with Cross-Site Request Forgery (CSRF);
        (2) requires use of Javascript
- **Reflected**
    - *Target:* user with Javascript-enabled browser who visits a vulnerable web service that will include parts of URLs it receives in the web page output it generates
    - *Attacker goal:*  run script in user’s browser with same access as provided to server’s regular scripts (subvert SOP)
    - *Attacker tools:* ability to get user to click on a speciallycrafted URL; optionally, a server used to receive stolen information such as cookies
    - *Key trick:* server fails to ensure that output it generates does not contain embedded scripts other than its own
    - *Notes:* 
        (1) do not confuse with Cross-Site Request Forgery (CSRF);
        (2) requires use of Javascript
        
### Protecting Servers Against XSS (OWASP)
- OWASP = Open Web Application Security Project
- The best way to protect against XSS attacks:
    - *Use White-listing:* Ensure that your app **validates** all headers, cookies, query strings, form fields, and hidden fields (i.e., all parameters) against a rigorous specification of **what should be allowed**.
    - *Beware Black-listing:* **Do not** attempt to identify active content and remove, filter, or **sanitize** it. There are too many types of active content and too many ways of encoding it to get around filters for such
content.

### Web Accesses w/ Side Effects
- Recall our earlier banking URL:
    - http://mybank.com/moneyxfer.cgi?account=alice&amt=50&to=bob
- So what happens if we visit evilsite.com, which includes: `<img5src="http://mybank.com/moneyxfer.cgi?555Account=alice&amt=500000&to=DrEvil">`
- Cross-Site Request Forgery (CSRF) attack

### CSRF For Email Tracking
- Application -- Boomerang. Injects JS in Gmail client. Email gets tracked and shows when/how often the other recipient opens it.
- Has `img style="display:none!important"` -- Broswer makes get request to that image URL, which will run something on the server that does something. but user can't see it so doesn't know what's going on. That code ensures that the image isn't displayed. 
- URL is proxied through google to maintain same origin 

#### Sources:
- https://docs.google.com/presentation/d/1M0OvUYpO2zYaVwI5k7TT84QWotSZfI7_snfqBtt9cd4/edit#slide=id.g1d3ccfab30_0_271
- http://www.icir.org/vern/cs161-sp13/slides/2.28.WebAttacks3.pdf
