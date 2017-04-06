---
layout: notes
title: Traffic Shaping for Electronic Surveillance
scribe: Freddie Vargus
---

# Talk Overview
1. The "collection abroad" loophole in US surveillance law
2. Using port mirroring to "shape" US traffic abroad
    - Technical analysis of an NSA slide revealed July 2016 legal implications
3. Using routing hijacks to "shape" US traffic abroad
4. Recommendations for surveillance policy
5. Recommendations for securing routing

**NSA shapes traffic, but not sure of the exact reasons.**



## FISA - Foreign Intelligence Surveillance Act (1978)

- Statute passed by 1978 Congress
- FISA is the "exclusive means" for "electronic surveillance"

"Electronic surveillance" under FISA is defined (roughly) as
1. Surveillance conducted on US soil OR
2. Surveillance operations that intentially target a US person

A lot of US Surveillance law is classified; hard to define things

### Oversight?
- More oversight in FISA
- There is a court that the programs need to go through; FISA is part of all 3 branches; a secret court (FISA court)
- The judges are from the judiciary branch
- If you violate this you have criminal liabilities

## Executive Order 12333

- Order signed by Reagan in 1981
- Self impose rules that the Executive follows when conducting surveillance
- A set of guidelines that they use
- No court oversight (just attorney general approved procedures)

### Electronic surveillance

"Electronic Surveillance" is a legal notion
- Surveillance of internet traffic that is collected abroad AND that does not "intentially target a US person"

Is exclusively under executive and not regulated by FISA, the FISA court, or Congress

Why is it different on collection abroad vs collection US? Well, we were in 1978 and 1981 when the laws were made; before the internet

#### What does "intentional targeting" mean?
Hard to understand; you want to target a specific person at the start but in order to get that data you need to pull it off of the entire fiber
and could "incidentally" collect information on millions/billions of other people

Speculation is that the NSA physically tapped the fiber that allows for the flow of traffic between countries

US person's data does leave the US

### What if the same info is collected under FISA & EO 12333

*"If a query result indicates that the source of information is both EO 12333 collection and PR/TT collection, then the analyst must handle the EO 12333 result according to the PR/TT rules".* Answer: **False**

You can collect data under 12333 rather than FISA
Has this happened?
The PR/TT collection was shut down; it "allows NSA to call chain from, to, or through US person selectors"
Allows metadata that's collected outside the US to be analyzed at will; if they found US person's data in what they found outside the US, then they could just use it

## Using port mirroring to shape the traffic abroad

First we have a fiber cable, which allows for the flow of network traffic. We like it when we can catch bad guys through traffic going through the cable.

But what happens when a target uses a service where the data _doesn't_ go through our access?

Adding a CNE midpoint fixes this to change the flow of traffic (Computer Network Exploitation aka hacking)

Now we can see the data, we just need to create a copy and send that copy through a fiber cable so it can get processed into the SIGINT system

![](https://prod01-cdn07.cdn.firstlook.org/wp-uploads/sites/1/2016/06/graphic.jpg?raw=true)


### Lawfully using port mirroring to evade FISA?

To use port mirroring to shape US Traffic abroad: a router on US soil must be hacked

Key question:
    - Does hacking into a US router & instructing it to port mirror traffic constitue "electronic surveillance" under FISA?


"electronic surveillance" defined by 50 US code 1801(f)(4) and 1801(f)(2)

### Is hacking a US router covered by the 4th amendment?

Is it a search? No. We just reprogrammed the router. We haven't looked at the traffic.

Is it a seizure? No. We just copied the traffic, but it still goes to its destination. Note: Orin kerr posits in 2010 that copying is a "seizure" if it "occurs without human observation and interrupts te stream of its possession and transmission"

### Internet routing with BGP

A network will send a message to its neighboring networks saying a block of addresses belongs to it; the first 19 bits are in common with each other between addresses

The links are setup ahead of time and are static and there is an ID for the specific group of nodes in the network

Subprefix hijack that "shaped" traffic abroad in July 2013; [longest prefix match routing](https://en.wikipedia.org/wiki/Longest_prefix_match)


### Surveillance policy recommendations

Update FISA so that it covers all tpyes of "electronic surveillance"
FISA Amendments Act up for renewal in Dec. 2017

1. Remove territorial distinctions
2. Update in a technologically-neutral way
3. Clarify the definition of "targeting" and "collection"


"New" network protocols for securing BGP

```
BGP -----| Resource Public key Infrastructure (RPKI) ---------- | BGPSEC
    	   - IETF Standard published in 2012	     		 - Builds on the RPKI
	   - Deployment started in 2011				 - Almost! standardized
	   - Certifies IP prefix allocations			 - Certifies announce routers
	   - Crypto done out-of-band				 - Crypto done online
	   - No change to BGP messages
```

We are trying to deploy RPKI and at are a low deployment rate right now, and BGPSEC is still in the works
