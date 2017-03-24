---
layout: notes
title: March 23, 2017: BugDrop; Pegasus; Cookies, XSS, and CSRF 
scribe: Daniel Valentine
---

# Lecture: March 23, 2017

## Group Presentation 1: Operation BugDrop (2017)

1. Background

	1.1. BugDrop malware discovered by the internet security firm CyberX in Feb 5, 2017
	
	1.2. most info on BugDrop based on CyberX report
	- "Given the sophistication of the code and how well the operation was executed, we have concluded that those carrying it out have previous field experience. While we are comfortable assigning nation-state level capabilities to this operation, we have no forensic evidence that links BugDrop to a specific nation-state or group." (Phil Neray, Vice President of Industrial Cybersecurity, CyberX)

2. Targets

	2.1. Nations
	
	- Ukraine (chiefly)
		
	- Russia
		
	- Saudi Arabia
		
	- Austria
		
	2.2. Organizations (70 per CyberX tally)
	
	- a company developing remote monitoring systems for gas pipeline infrastructures and oil
		
	- engineering company designing gas distribution pipelines, electrical substations, and water supply plants
		
	- counterterrorist monitoring organizations on the lookout for attacks, both physical and digital, on “critical” Ukranian infrastructure
		
	- a scientific research institute
		
3. What is BugDrop?

	3.1. software that drops a “bug” (in the classical surveillance sense) to collect information from a victim and their computer
	
	3.2. performs the following actions:
	
	- keylogging
		
	- audio recording
		
	- file transfer
		
	- browser history monitoring
		
4. Architecture

	4.1. High-level overview
	
	- Microsoft Word "Decoy" Docx with embedded VBS (to extract payload, see <a href="./#BDSECTION421">§4.2.1 Main downloader: the "Dropper"</a>)
		
	- Stage0.exe (persistent in registry)
			
	- Stage1.dll
			
	- Stage2.dll (persistent in registry); reports to `windows-problem-report[.]site88[.]net`
			
	- Main module encrypting data, sending it to Dropbox (see <a href="./#BDSECTION5">§5 Functionality of Main Module</a>)
			
	4.2. Medium-level overview
	
	- <a name="BDSECTION421">Main downloader: the "Dropper"</a>
		
	- Stage 0: VBS script in Docx extracts the first part of the payload, Dropper.exe, from the MS Word archive; Payload obfuscated via simple technique of XORing each byte with the previous; to recover, repeat XOR process
		
	- Stage 1: Achieving persistency (.dll) (ensuring DLL remains in system, even after a shutdown or restart); inserts itself into registery at key `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run\drvpath`
		
	- Stage 2: downloads the Main Module Dynamically-Linked Library (DLL) and performs a DLL injection; malware executes a *reflective* DLL injection: the basic idea of a reflective DLL injection is that the DLL library is responsible for loading itself without making calls to the Windows API

	- Main Module (see <a href="./#BDSECTION5">§5 Functionality of Main Module</a>)
		
5. <a name="BDSECTION5">Functionality of Main Module</a>

	5.1 Performs following steps:
	
	i. achieve persistency
	
	ii. download from malicious site
	
	iii. check for anti-reverse-engineering software present on the system, e.g. look for: a debugger; ProcessExplorer, or Wireshark; or any evidence a process is operating in a virtualized environment
	
	iv. download plugins to retrieve: local files; files on shared or external drives; browser info, including passwords; audio from microphone; and any extra details about victim device
	
	v. encrypt and distribute stolen data
	
	5.2 output of data encrypted with Blowfish (symmetric key block cipher) and sent to `%USERPROFILE%\AppData\Roaming\Media`
	
	5.3. data uploaded to Dropbox; after attackers retrieve data, they remove it from the Dropbox server

6. Primary recommendation against similar attacks: __disable Microsoft Word macros!__

## Group Presentation 2: Pegasus iOS Spyware (2016)

1. Background

	1.1. Ahmed Mansoor, a UAE national, receives two supicious texts from a spoof phone number, containing links
	
	1.2. Mansoor sends the texts to the mobile security firm Lookout and a research body based at the University of Toronto, Citizen Lab
	
	1.3. Security researchers load the links on a vanilla, factory-reset iPhone 5 running iOS 9.3.3. and observe that the link opens a blank page on Safari; the phone hangs for 10 seconds as malware is deployed on the phone, and promptly closes afterward

2. What is Pegasus?

	2.1. Pegasus is spyware intended primarily for government authorities to commit "lawful interception"
	
	2.2. Designed by NSO Group, an Israeli firm, who has expressed that the software is designed for crime prevention/investigation and should only be used for that purpose
	
	2.3. Operating Systems susceptible to Pegasus exploits
	
	- iOS >= 9.3
	
	- OS X >= 10.11 "El Capitan"
	
	2.4. Targeted data include: texts and phone calls; audio captured by the microphone; emails and passwords; calendar; third-party apps
	
	2.5. Attacker likely UAE and Mansoor was probably their target
	
3. Technical Breakdown of Spyware

	3.1. Pegasus exploits a "Trident" of bugs to gain kernel privileges (see <a href="TRIDENT">§4 The Trident in Further Detail</a> below)

	3.2. SMS sent via anonymizing servers hiding attacker's identity
	
	3.3. replaces third-party dynamically-linked libraries with custom libraries to steal data
	
	3.4. restricts accesss through other means if device is already jailbroken
	
	3.5. emphasizes stealth; Pegasus can delete itself if detected
	
4. <a name="TRIDENT">The Trident in Further Detail</a>

	4.1. [CVE-2016-4657](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2016-4657): WebKit Memory Corruption
	
	- attacker needs to run code on iDevice
	
	- when user opens link to malicious webpage, Javascript begins execution and uses 4657 to access kernel
	
	- now, attacker permitted to execute arbitrary code
	
	4.2. [CVE-2016-4655](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2016-4655): Kernel Information Leak
	
	- iOS uses KASLR (Kernel Address Space Layout Randomization) at startup to place kernel code in a random area of memory, providing a rudimentary layer of security
	
	- 4655 is a bug that causes the kernel to leak kernel stack information, with which the adversary can use to compute the kernel address (in addition to using a kernel function return address)
	
	4.3. [CVE-2016-4656](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2016-4656): Kernel Use-after-free
	
	- 4656 is a "use-after-free" vulnerability allows an adversary to discovery the location of the kernel
	
	- During runtime the kernel will reserve memory for various objects, and pointers direct kernel to the locations of these objects
	
	- If kernel deallocates memory for object but does not destroy pointer, an adversary can use the pointer to get access to a region of kernel memory to store arbitrary code; this is the "use-after-free" vulnerability
5. Recommendations for Prevention

	5.1. beware of suspicious links, text messages, and emails
	
	5.2. update to the latest version of iOS/macOS
	
	5.3. use Lookout antivirus software on iOS
	
	5.4. contribute to the Apple Bug Bounty program

## Lecture: Web Hacking II (Cookies; SOP; XSS; CSRF)

___Important:___ Get the EditThisCookie Chrome extension for Lab 4.

1. What is a cookie?

	1.1. A cookie is a key-value store on a user's browser to maintain information about session information, e.g.: remember a user's shopping cart, identify a user with a login session, identify a user across multiple websites (expounded in <a href="#COSECTION4">§4 Third-Party Cookies</a> below)
	
	1.2. Decentralizes storage of session information so that a server does not need to keep track of them (imagine the space cookies can quickly consume were this the case)
	
	1.3. Cookies will be included in all future browser `GET` requests

2. Setting Cookies
	
	2.1. `Set-Cookie` headers in HTTP request, containing:
	
	- `KEY=value` pairs, aka. a "Cookie crumb"
	
	- `Expires`, an expiration date; if not set, the cookie is a _session cookie_ (see <a href="#COSECTION222">§2.2.2</a>)
	
	- `Domain`, which gives specifies who is permitted to access the cookie  
	
	Nota bene 1: ``.foo.com`` is equivalent to ``foo.com``, which means in both cases the subdomains of ``foo.com`` will be permitted access to the cookie. But...  
	
	Nota bene 2: only the ``specific`` domain in cookie can access cookie! This means that ``bar.foo.com`` cannot access a cookie meant for ``fubar.foo.com``
	
	2.2. Flags also specified in header
	
	- `HostOnly` -- cookie can only be read by same domain that set it; e.g., `foo.example.com` sets it so `bar.example.com` cannot read this cookie if `HostOnly=1`
		
	- <a name="COSECTION222">`Session` -- session cookie expires when user navigates away from website</a>
		
	- `Secure` -- cookie can only be sent through HTTPS
		
	- `HttpOnly` -- cookie _cannot_ be accessed by Javascript; preventative measure against cross-site scripting (XSS)

3. <a name="COSECTION3">Zombie Cookies</a>

	3.1. a "zombie" cookie is a cookie recreated automatically after deletion
	
	3.2. accomplished by storing cookie content in multiple locations; common examples:
	
	- Flash local shared object
	
	- HTML5 web storage
	
4. <a name="COSECTION4">Third-Party Cookies</a>

	4.1. used for ad-tracking across different websites
	
	4.2. most popular browsers (Firefox, IE/Edge, Opera, Chrome) permit creation and use of third-party cookies so long as the third-party site has Compact Privacy Policy published; newer verisons of Safari block third-party cookies
	
	4.3. How are third-party cookies established?
	
	4.4. typically a(n) `<iframe>` tag which includes content from external source, e.g. a complete HTML page with Javascript code to store cookie in persistent storage to recover upon actual cookie expiration (see <a href="#COSECTION3">§3 Zombie Cookies</a>)
	
	4.4. 2012 EU mandate requires websites to warn users about third-party cookies the websites (may) use

5. Javascript (no relation to Java)

	5.1. Javascript is a powerful web page programming language, typically used for scripts embedded in web pages which the browser executes
	
	- can be used to create a dynamic web page; rather than static HTML, web pages can be expressed as a program, say written in Javascript
	
	5.2. given Javascript's power, browsers must make sure scripts cannot abuse power; enter the Same Origin Policy

6. Same Origin Policy (SOP)
	6.1. simplified description of SOP
	
	- Browser associates web page elements (layout, cookies, events) with a given origin with the web server that provided page/cookies in the first place, and browser only allows HTTP requests to that same "origin"
	
	6.2. technical definition of "origin"
	
	- `protocol://host:port/`
	
	- host MUST match exactly with domain content in cookie, including port information; e.g., `www.example.com` is different from `example.com` or `www.example.com:81`
	
	6.2. under policy, subdomains are _not_ included

7. Cross-Site Scripting (XSS): subverting the SOP

	7.1. Simplified description of XSS
	
	- It would be bad if an attacker from another domain can fool a user's browsers into executing script of their choice on _your_ domain
	
	- one nasty/general approach is XSS: trick server of interest to send attacker's script to user's browser
	
	7.2. The two categories of XSS
	
	- __Stored__: attacker leaves script lying around on target "patsy" server, who unwittingly sends it to a browser accessing server; e.g., adding comment on page, status on Facebook, heroes section on MySpace
		
	- can steal cookies
	
	- key trick: server fais to ensure that uploaded content to page does _not_ contain embedded scripts
	
	- do _not_ confuse with CSRF (see <a href="#CSRF">§7.4 Cross-Site Request Forgeries (CSRF)</a>)
	
	- __Reflected__: attacker convinces user to send target server a URL that has Javascript crammed into it, and server echoes it back to you in response
	
	7.3. Protecting servers against XSS
	
		- Best way to protect against XSS attacks per OWASP (Open Web Security Project) is whitelisting
			
		- validate all headers, cookies, queries, strings, form fields, hidden fields against a rigorous specification of what _should_ be allowed
			
		- avoid blacklisting
		
8. <a name="CSRF">Cross-Site Request Forgeries</a>
	
	8.1. browser making a `GET` request to some other source _not_ within the same source
	
## Acknowledgements

Sean Smith and the students provided me copy of their lecture slides after class time. These notes incorporate information from these slides.

1. Ezequiel Gomez, Omar Sagga, Tara Taybah, Keyan Vakil (BugDrop)

2. Adonica Camp, Freja Mickos, Doug Roeper, Cyril Saade: for providing me (Pegasus)
