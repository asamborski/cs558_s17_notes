---
layout: notes
title: Encrypted Message System
scribe: Bohan Li
---

Check Schedule for slides and other material used for this class.

# End-to-End 

When starts a new conversation with someone on Whatsapp, a user will see a message like this: “Messages you send to this chat and calls are now secured with end-to-end encryption” 

Unlike encryption in transit (TLS, email), end-to-end encryption is a system where only the two clients can read the messages. The service providers will only receive encrypted payloads (However the metadata is still visible for service provider).

The newest model of e2e encryption does not employ a service provider. The two clients will talk directly through a encrypted channel. 

# The OTR 3-step Diffie-Hellman Ratchet

This is a historical protocol that provides ‘Forward Secrecy’. 
In Diffie-Hellman we use g^a, g^b as keys to encrypt and decrypt, use sign_sk(g^a,g^b) to make sure the message was sent to the right person.
In this ratchet, even if the secret key and the encrypted conversation are compromised in the future, the conversations in the past cannot be decrypted. This is because the session key a and b will be deleted after the session ends. And without a,b it is impossible to decrypt.
A backdoor to this protocol is backups to third parties such as iCloud.
Why people don't like it? If one user in the conversation does not read the message after receiving it, the session key will be kept for days.

# Asynch Hash Ratchet

In this protocol, keys have sequence numbers:
k1 = H(ms)
k2 = H(k1)
k3 = H(k2)

Therefore, once the key is compromised, it is possible to decrypt message in the future (new keys are just hashes of the old keys). But it’s impossible to decrypt message in the past.
Drawback (from the slide): The key material is sensitive. However, it can be used to calculate the key material for every subsequent sequence number. So a client can’t hang on to it forever.

# Signal 2 step DH Ratchet (“Chain Keys”)

Here is the procedure of this protocol
A generates a new ECDH ephemeral key A1 and use it immediately to send a message. A then receives a message with B’s key B1. To send a new message, A will destroy the key A1 and create a new key A2 and so on.
A hash-based ratchet is added between chain keys.


# Metadata leaks information

By looking at just metadata, we can collect information such as source address, destination address, frequencies, etc.

Key distribution is a big problem while contacting people on apps such as Whatsapp. How do we get the key? How do we know the key is real? And who has access to the key? Assume there are two users A and B. If A and B have never had a conversation before, then A must require B’s key from Whatsapp. How does A know the key belongs to B? Attacks can be generated in this process.

# Tor: The Onion Router
It prevents people from knowing your metadata.

Tor is based on a problem called “Dinning Cryptographers Problem.”

Assume there are mixes sitting between sources and destinations. The mixes will randomly permutes and decrypts inputs. In this way people don't know which input matches which output. If the mixes do not cooperate (i.e. not all of them are compromised), it’s safe.

While using TOR, the message is actually encrypted in multi-layers and hopped around many nodes. Suppose there are three servers s1, s2 and s3. There will be three keys k1, k2, k3 corresponding to the servers. The message is encrypted in Enc_k1(Enc_k2(Enc_k3(m))). Therefore, if the attack does not know all of the keys and the order of servers, the message cannot be decrypted.

One problem is that not all messages come at the same time. If there is only on message, the permutation won’t work and the attackers could start a “Timing Attack”.

To prevent this kind of attack, relays are used.
The simplest design uses a single relay to hide traffic. However, if this relay is compromised, the attackers will be able to see the traffic. Therefore, multiple relays are used. Assume A and B are going to have a conversation, and there are three relays R1, R2, R3 between them. R1 will know that A is talking to R2; R2 will know that the traffic from R1 goes to R3; R3 will know that the traffic from R2 is going to Bob. If the three relays are not compromised at the same time, no one knows that the traffic is from A to B.
To start, A first makes a session key with R1, then tunnels to R2 and R3. Then talks to B over circuit.

One point to notice is that the last relay will always know the content, if the content is not sent through HTTPS.

Therefore, while using TOR, all communications must be under TOR, such as DNS lookups. Focus of TOR is anonymity of the communications pipe, instead of the data passes through it.


