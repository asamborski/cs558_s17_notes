---
layout: notes
title: "Traffic Shaping for Electronic Surveillance"
scribe: Ines Kim
---

## Overview
There exist loopholes in current US surveillance laws: different rules apply to collecting internet traffic abroad vs domestically
How can someone (like NSA) purposefully "shape" domestic traffic to occur abroad so as to apply the different set of rules?
...*NSA shapes traffic, but the exact reasons why aren't clear

# FISA - Foreign Intelligence Surveillance Act (1978)
... What "electronic surveillance" (domestic) means
......*surveillance on US soil or "intentionally targetting a US person" (a phrase which is a headache to understand in itself)
... Surveillance laws are difficult because some of them are private
...FISA is a part of all 3 branches of government
......*FISA secret court is from the judiciary branch as an _oversight_ of programs
......*includes criminal violations

# Executive Order 12333 (1981)
... a set of rules the executive branch conducts when surveilling
... Not subject to FISA court oversight
......*only approved by the attorney general
......*without criminal violations

## Traffic Abroad
...Essentially the "not" of the FISA laws
......*surveillance collected abroad
......*not intentional targetting
...It is exclusively under the Executive Order 12333, therefore not regulated by FISA/FISA court/Congress

## "Intentional Targetting"
...not really clarified in the laws
...intentionally targetting some person implies a _huge scope_ because of "incidental data"
......if a target sends an email, which is stored in some data center, all other emails stored in that data center are collected as "incidental data"


------------------       ---------------------
| user <--ssl--> |       |     data center   |
|                |_[GFE]_| data center       |
| user <--ssl--> |       |      data center  |
------------------       --------------------

...GFE = Google Front End server
... It used to be that Google's (and other websites) data centers would communicate in _clear text_
... So NSA would physically tap into the wires between Google's data centers to see the traffic
......*these taps were in the UK, not the US
......*the physical layers aren't owned by Google, but leased by another company
... Once this became news, Google and other companies now encrypt this data

## NSA training module
... In a leaked picture of a question from an NSA training module, the question asked:
...... if a query result falls under the 12333 or FISA, which set of laws should be applied?
.......... the answer is: 12333, the less strict of the two

## Domestic Email Collection Program
...*was a program that fell under FISA
...* collected email metadata
...* was shutdown for reasons including: allowing NSA to get info from select US people

# Using port mirroring to shape traffic abroad
... suppose the NSA wanted to view the traffic of a target (let's say an evil actor) who is communicating with a server.
... The NSA deplys their CNE man (their term for hacking) to bounce the traffic of the target to a wire _abroad_ that the NSA is currently tapped into.

Is this legal? i.e does electronic surveillance via port mirroring fall under FISA
... FISA clauses on electronic surveillance:
......*a permit is required for installation to everything except wire and radio
......... internet travels on a wire
......*"...if such acqusition occurs in US..."
.........where does the aquisition happen? does it happen when the traffic is bounced, or when it is actually observed (which is abroad)


## 4th amendment and hacking a US router
...search?
......-no just reprogramming a router, no looking at traffic
...siezure?
......-no, just copied. it still goes to intended destination
...trespass?
......-maybe, to us at least
But the point is that we don't know how the government interprets it

# another way to traffic shaping - BGP subprefix hijacking
## Hijacking example
... There is a computer at a router _Atrato_ that wants to talk to a computer at IP 206.51.69.20 at router _AS 22561_
... Each router announces a prefix, simply put a range of IP addresses. SO _AS 22561_ announces it has 206.51.64.0/19, meaning it has addresses that match the first 19 bits
... So the path would be [206.51.69.0/19 AS 22561]

... What happens in subprefix hijacking is..
... 206.51.64.0/24 is a more specific prefix match, because it account for more bits in the IP. This is a subprefix of the prefix 206.51.65.0/19
... By BGP protocols, a router that advertises a more specific subprefix should the the path taken
... A successful subprefix hijack leads the router taking a path that is longer than it should go - and potentially takes a roundabout way leading into routers abroad.

... So if a router called Evil announced a path [206.51.69.0/24 AS 3V1L], that would be the path taken by Atrato - intead of the correct path [206.51.69.0/19 AS 22561]

## IETF - RPKI
... making unhackable networkd devices is really hard
... it's also really hard to secure BGP
... IETF has introduced RPKI - Resource Public Key Infrastructure to try to secure BGP
...... it works by having a local database of signed routing announcements. Thus, if a malicious router announces a subprefix in an attempt to hijack, if that announcment doesn't match the signed announcements in the databse, it is discarded
...... this can still be subverted if the malicious router announces a a valid prefix (not a subprefix) because there is a chance the path including it can be taken
...... if a router had to choose a path between two correct prefixes, it would take the path of "least cost"
......... because the travel of traffic between routers are often paid for (like BU paying AT&T to handle internet services), if another router advertises a cheaper or free option was available, that would be chosen
There is another protocol proposed by IEFT called BGPSec which includes each router signing a path and prefixes, however it is very expensive to implement.














