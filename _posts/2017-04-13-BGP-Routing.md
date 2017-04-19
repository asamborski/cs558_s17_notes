---
layout: notes
title: BGP-Routing 
scribe: Aswin Vasudevan
---
Scribe Notes 
Dated : 04/13/2017
Submitted by : Aswin Vasudevan

Security Presentation: OPM 
1.	Federal office information
2.	OPM Server-> Active Directory (Microsoft Windows)
Access : Active Directory Privilege
NTLM Authentication Attack:
Step1 : Getting login using MimiKatz hash!
Step2 : Login with Hash and pass the hash attack
Step3 : Establishing Back door : Remote Access Trojan 
Prevention Methods: 2 factor Security, Deep panda work (Govt funded). 
 
Internet Routing with BGP:
Say , Server : Qwest / Century Link Denver 206.51.69
Normal Scenario Flow : Denver -> Dallas -> Kansas -> Denver
Attack Scenario Flow : Denver -> Iceland -> Chicago -> Denver (loop) CONVOLUTED NETWORK !
 
when packet falls in between 19 and 24 , it will be sent to the specific , so 24 can be specific in that case discussed in the class. 

The packets that are going outside USA and the router gets into BLACK HOLE state. These are seen in reference to the 4th amendment act.

Note
Rensys found the problem with BGP using “ Trace Route”  which checks the packet route when it was simply sent between two PC in Denver the trace seems to go all the way to Iceland to reach the second PC.

Securing in BGP
 
BGP    RPKI   BGPSEC      KEY 
RPKI :								                  BGP SEC
IETF statement published in 2012			 Builds on RPKI
Certifies IP						               Certifies announced routes
Crypto done out of band				         Crypto done online
No change to BGP              				 Major change to BGP

Note : Since this method does everything in Public Key its expensive and SLOW!

Signing the Path FIXES BGP
Cogent Validates using ROA. Downside of this method is that huge processing time for the routers.

In case of ROA- Router Origin Authentication

On server AS 22561 206.51.64/19   RPKI   Marks RPKI valid for /19 and Invalid for /24 

If no ROA , then route is usually based on cost and link. 

Since we only pay for packets from one point to another point any other route the packet takes is not considered.  So the agreement is that QWEST should have the paths where the BGP goes. 
In our case  that Iceland signature should be with  Qwest to send which is not possible hence the fix.

Circumvent the Protocol & Still Attack : 

World 1 : BGPSEC ONLY!
World 2 : BGP ONLY 
So to communicate you need both BGP and BGP SEC which pays forward to Downgrade Attack. 
Atrato is doomed when it prefers the cheap routes over secure route. 
