---
layout: notes
title: Encrypted Messaging Systems 
scribe: Karan Varindani
---

_Signal_ was one of the first encrypted apps developed by cryptographers, intended for research (not profit). _WhatsApp_ also uses its encryption protocol. Read more about the _Signal_ protocol [here](https://whispersystems.org/blog/advanced-ratcheting/), and view the lecture slides [here](https://www.cs.bu.edu/~goldbe/teaching/CS558S17/Messaging.pdf).

**End-to-End Encryption** involves two users communicating over an encrypted channel. It differs from encryption in transit (e.g. TLS, email) where the service provider (e.g. Google) can see your traffic but your connection to and from the provider is encrypted.  With end-to-end encryption however, the service provider can still see the metadata (the sender and receiver, the frequency of the emails, etc) — but we don't care too much about that (for now).

## The OTR 3-Step Diffie-Hellman Ratchet
With Diffie-Hellman, even if the secret key and previous communication was exposed, the session keys that were used would have since been deleted so the communication cannot be decrypted.[^1] _Signal_ doesn't like this protocol because if the sender sends a message to a receiver and the receiver doesn't read the message for a few days, the sender has to keep the session keys alive for that amount of time.

## Asynchronous Hash Ratchet
	K1 = H(m)
	K2 = H(K1)
	K3 = H(K2)

The key material is sensitive because it can be used to calculate the key material for all subsequent sequence number, so the client shouldn't hold on to it indefinitely (you can't uncover previous keys though, only subsequent keys). 

## Signal's 2-Step DH Ratchet ("Chain Keys")
1. Alice generates a new ECDH ephemeral key `A1` and uses it immediately to send a message.
2. Alice receives a message with Bob's new ECDH ephemeral `B1` and can then destroy `A1` and generate `A2` when sending her next message. 

This solves the problem of keys staying alive while the conversation is still alive (which for most of us it usually is). 

## Back to the metadata
We can learn a lot by just taking data, turning it into a graph, and observing it. The NSA director is on the record as saying "We kill people based on metadata" — so it should be a big deal.

From [WhatsApp's Privacy Policy](https://www.whatsapp.com/legal/):
> WhatsApp may retain date and time stamp information associated with successfully delivered messages and the mobile phone numbers involved in the messages, as well as any other information which WhatsApp is legally compelled to collect.

What if we ping the _WhatsApp_ server for someone's public key in order to message them. How do we know that this is their actual key, and not some key that a third-party has access to and can use to monitor our conversation? We have no real way of verifying this.

We now shift focus to analysis of _Tor_'s encryption protocol. It's based on the [Dining Cryptographer's Problem](https://en.m.wikipedia.org/wiki/Dining_cryptographers_problem) from the 1980s. _Tor_ stands for 'The Onion Router' because of all the 'layers' of encryption. Your communication hops from one _Tor_ node to another so even if an attacker compromises a node[^2], communication is still encrypted. The attacker needs to know the order of nodes in which the message was passed through and then compromise each node in order to decrypt the message. 

_Tor_ needs to be used over HTTPS. If you use _Tor_ and you're communicating over HTTP, your full communication will be visible to the final exit relay. 

If you have an anonymity system that nobody uses, it's useless. See the case from last year where a Harvard student sent in a bomb threat and was caught because only 3 users were on the Harvard _Tor_ node at that moment. 

To complete the circle, one of the reasons for _Tor_ was to hide the metadata. By hopping through a number of nodes, it's unknown who the original sender or final recipient are. A lot of metadata is lost in the hop.  

---- 

[^1]:	As an aside, the NSA has permission to keep encrypted communication they intercept indefinitely.

[^2]:	The FBI recently compromised 120 _Tor_ nodes, for example.
