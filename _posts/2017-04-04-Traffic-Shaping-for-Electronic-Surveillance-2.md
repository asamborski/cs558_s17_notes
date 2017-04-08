---
layout: notes
title: Traffic Shaping for Electronic Surveillance
scribe: Darryl L Johnson
---

## Presentation : Cloudbleed

### What is Cloudflare?

Content Distribution Network.

HTML parser created by Cloudflare has a memory leak bug that caused leaked data to be served to end users and cached by search engines. 

### HTML Parser

State machine that parses HTML, looks for closing script tag and if not found, returns data that was not allocated for this process but from outside the buffer. Due to the buffer overflow, raw data was directly injected into the displaced HTML that was sent to users. Because search engines (Google, Bing, etc.) cache pages after crawling, this sensitive information that was leaked was available in search results.

### How it works

Malformed HTML tags like this would trigger the bug:

```html
<html>
  <head>
    <script type=
  </head>
</html>
```
  
*this would trigger the buffer overflow*

Welformed HTML such as this wouldn’t trigger the bug:

```html
<html>
  <head>
    <script></script>
  </head>
  <body></body>
</html>
```  

### Prevention
- Tagged memory
- Sandbox servers
- Evaluate legacy code thoroughly (parser was 7+ years old)

## Traffic Shaping for Electronic Surveillance

US surveillance law is written in such a way that if you tap a cable inside the US vs outside the US, different laws apply. 
- The “collection abroad” loophole in U.S. surveillance law

### Foreign Intelligence Surveillance Act (FISA)
- Statute passed by 1978 Congress
- The “exclusive means” for “electronic surveillance”
Where electronic surveillance under FISA is defined as 
  1. Surveillance operations conducted on US soil 
			
      OR
      
  2. Surveillance operations that “intentionally” target a U.S. person

**Oversight?**
- Programs must be authorized by the secret FISA court

### Executive Order 12333
- Order signed by Regan in 1981
- Self-imposed riles Executive follows when conducting intelligence activities beyond those regulated by Congress
- The NSA stated that it conducts the majority of its SIGINT activities solely pursuant to the authority

**Oversight?**
- No court oversight (just AG-approved procedures)

<br />

**What matters for us?** The notion of “electronic surveillance” (from above)

Surveillance of internet traffic
- that is collected abroad, AND
- that does not “intentionally target a U.S. person”

is exclusively under Executive Order 12333, and not regulated by FISA, the FISA Court, or Congress. 

If data is queried and served both from a FISA collection and an EO 12333 collection, the less strict (EO 12333) applies. If you can ensure data (that was originally routed from or within the U.S.) is collected outside the U.S., Executive Order 12333 is the statue to be followed and allows less stringent and less regulated collection of U.S. persons. 

### Using Port Mirroring to Shape Traffic Control
The NSA does in fact re-route traffic when it suits their intelligence purposes. 
1. NSA has a specific (or many specific) fiber cable taps. This is because tapping a fiber cable is a major operation and can not be moved easily.
2. If the data the NSA wants to collect runs through their wiretap, they can simply collect the data directly from the tap. 
3. If the data that the NSA wants to collect does not run through their taps, they redirect (copy) traffic to run through their tap through some other exploit

**Lawfully Using Port Mirroring to Evade FISA?**
To use mirroring to shape US traffic abroad you need:
- A router on US soil to redirect traffic abroad

### BGP Main in the Middle to Shape Traffic Control
Instead of hacking a router to redirect network traffic abroad (as well as to it’s destination), a perpetrator could send a BGP advertisement to redirect traffic from one network to their network. Then after receiving the packets on their network, the data can be directed to the destination network. 

**How to fix?** BGPSEC

#### Surveillance Policy Recommendations
- Update FISA so that it covers all types of “electronic surveillance”
- FISA Amendments Act up for renewal in December of 2017. 
  1.	Remove territorial distinctions
  2. 	Updates in a technillogically-neutral way
  3. 	Clarify the definition of “targeting” and “collection”
