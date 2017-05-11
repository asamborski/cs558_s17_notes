---
layout: notes
title:  "DNS with Reyzin"
scribe: Benjamin Li
---

Defensive and Offensive DNS Tactics

You can just remove or resent an entriy in DNS if your trying to
filter certain content, this removes don’t need complex firewalls-You
can even exploit DNS connected devices for amplify your requests into a
DDOS attack

Kaminsky Attack

It utilizes the Query ID, a 16 bit number, or two bytes. These only have 65,536 possible out comes, so they’re bruteforceable in the time it take for the correct QID to come back. If the query is brute forced before the genuine one comes back, the attackercan poison a victim’s DNS cache.  So you can resolve queries, but if there’re two of the same it makes a race conditionTO do this more securely, you can link someones web page to a source of your choosing.  You’d know the QID of the chosen page, so you can predict it.

Easy case for the Attacker
Fixed Port←--------------------| learn these 2 when visiting attackers innocent web site is visited
Sequential ID ←——————- |b/c DNS will go to attackers name server




|aaxyz.bu.edu |} nonexistent weird name
|aaxy0.bu.edu |} nonexistent weird names 
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  Add infinitum
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  
Many guaranteed esponses will redirect to another name server operated by the attacker.


GLUE RECORD


|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;bu.edu NS ns1.bu.edu &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | \<— Correct 
| ns1.bu.edu A Attackers IP address | \<— Fake Glue record

This means that even though the chances of poisoning are slim, the benefit of
succeeding is correspondingly huge.The solution is QID randomizing,
which gives you 2\^16 bits of security. Though, the kaminsky attack
still takes 10 seconds. So, you add port randomization to get another 11
bits of security, bring the total of 2\^27 bits of security.DNS SecPKIs
sign the MAC codeDNSsec adds a new record type called RRSIG (resource
record signature)This is a signature for all the A recordsNew Record
type DNSkey public keyYou get some weird chicken and egg problems in
regards to signing the public key, and signing the signature, and
signing that signature and so on. 


DIAGRAM of PKI Hierarchy

        ____PK____
        |         |
        |       com PK com
        |
    edu.pk.edu
        |
        |
    bu.edu PK bu.edu

PKI is the biggest security problem, but solving the biggest problem is time consuming so you do what you can.2 keys at every stepKey Signing Key(KSK) ---- Zone Signing Key(ZSK)Each zone has 2 DNS key records, 1 for KSK and 1 for ZSKYou can do frequent chance ups for the ZSK, but you can’t do do frequent change ups to KSKRoot KSK signing ceremony is some wacky stuff, like spy level wacky stuff.  People go through faraday cages and all that Hi security stuffNSEC (next secure)Contains the next name in alphabetical order to prove that a name does not existInsecurity: you can query a bogus name, and get two names.  Then use each one to find the next via nsec.  It’s called zone walking

