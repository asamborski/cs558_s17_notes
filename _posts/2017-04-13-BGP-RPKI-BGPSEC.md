---
layout: notes
title: BGP, RPKI, and BGPSEC
scribe: Seunghun Oh
---
# Presentation: Office of Personal Management Hack
### Presented by Ben Li, Seunghun Oh, Cyril Saade

TL;DR: The United States Office of Personal Management suffered data theft of 21.5 million individuals who were applying or related to individuals applying for security clearance. Hack was done by taking advantage of the Active Directory system as well as a Remore-Access Trojan.


#### Background on Office of Personal Management

OPM is an independent organization taht anages civil employeers of the federal government. They are responsible for 2.66 million federal employees and their data.

OPM's servers ran Microsoft Windows Server Platform, which has an Active Directory Architecture (AD). OPM's Ad included a user forest, which categorizes low clearance, low information accounts and administrator accounts in varying levels.

A year before OPM's hack, KeyPoint (a government solutions group responsible for issuing clearances for government workers) experienced unauthorized access into their system. KeyPoint is part of OPM's user forest, meaning that the hackers can access the entire user forest with the right account access and authorization.

However, this was bypassed with Active Directory Privilege Escalation. When prompted with a login page, NTLM Authentication Protocol is 
run. This protocol operates LSASS.exe (Local Security Authority Subsystem Service) to save users' credentials to memory and to create access tokens. This executable bypasses the need to have credentails to climb the OPM's user forest.


#### The Attack

AD Privilege Escalation was performed using two vulnerabilities of the Window Server: LSASS, which was created by French security researcher Benjamin Deply. This program is also commonly known as Mimikatz. Mimikatz was run remotely using remote control programs that were installed onto the system. Mimikatz then retrieves the hash of the password.

Next, the attackers were able to identify and target administrator accounts in the AD user forest. Then, they compromised their account using NTML Authentication. The attackers performed a "Pass-the-hash" attack, which bypasses the need to actually get the plaintext of the password. The attackers have the hash of the password due to Mimikatz.

After logging into various accounts, the attackers are able to determine the user that has the highest priviledges in order to install their exploit: a backdoor through a Remote Access Trojan: PlugX variant. The RAT installs mcutil.dll, a standard software sold by security giant McAfee. The dll talks to an untrusted website (opmsecurity .org) using POST requests.


#### Consequences of the Hack

Due to the hack, 19.7 million security applicants' data were compromised. 1.8 million co-habitants of applicants had their data compromised as well. 5.6 million unlucky individuals had their fingerprint data stolen. Majority of the data stolen were information such as addresses, dath of births, pay histories, pension informations, age, gender, and race. This breach led to a 30-Day security sprint.

The head of OPM, Katherine Archuleta, was forced to resign.

The hack could have been prevented using 2-Factor Security or changing compromised credentials after the KeyPoint Data Breach.


#### Who did it?

There is no clear agreement on who performed the hack. However, some people speculate that the Chinese government funded group Deep Panda may have conducted the attack.



# BGP, RPKI, and BGPSEC

### Subprefix Hijacking
We can create a man-in-the-middle (MITM) situation by using what is called subprefix hijack. Subprefix hijack “shaped” how traffic is communicated abroad in July 2013. The hijack takes advantage of the fact that routers tend to choose the more specific IP address.

A “specific” IP address is one that tends to have less open address if it has a bigger prefix. A prefix of an IP address is denoted by …/x, where x is an integer that denotes how many of the bits are used to denote an address. This means the available number of reserved blocks is equivalent to 32 – x. If there are less blocks, the IP address is considered to be more specific.

A hijacker who wishes to perform a MITM eavesdropping can do so by inserting data with a more specific prefix, overriding the victim’s IP address.
The man-in-the-middle must be careful with where he enters the IP address and AS data; otherwise he may create a “black hole” of data. He can avoid a “black hole” by making sure packets are sent to a victim’s receiving router.


### BGPSEC, RPKI, ROA
To prevent having a MITM situation, we can set up a Resource Public Key Infrastructure (RPKI) in the domain routing with Route Origin Authentication (ROA). A regular domain structure is set up as BGP. RPKI validates the origin of the data. For RPKI, crypto is done out of band, no changes are made to BGP messages, and certifies IP prefix allocations. 

Before RPKI is implemented, IP address can be forged; however, RPKI makes sure that the IP address cannot be forged. This data is secured with a ROA to make sure data that is sent is accurate. Note that if the exact IP address and AS data is used, then the path can be shortened and data can be sent to the victim with RPKI verification.

However, this path shortening can be prevented by using a BGPSEC. BGPSEC builds on RPKI, almost standardized, certifies announced routes, crypto is done online using public keys, and major change is applied to BGP messages.

When RPKI is implemented to a BGPSEC, the IP address and AS is encrypted with RPKI. This means both path shortening attacks as well as IP spoofing attacks no longer work. However, BGPSEC is not a standard, because it costs more money than an unsecure path. In order to accommodate both BGPSEC paths and BGP paths, downgrading must be possible. Thus, downgrading attacks can occur. Network managers (when asked on a survey performed by Professor Goldberg) preferred the cheaper and unsecure path over the secure path.
