---
layout: notes
title: Certificate
scribe: Li Liu
---

### SHA Collison
Bank of America: AES_256_CBC_SHA (SHA 1 with HMAC)

Google: AES_128_GCM_SHA256



#### What is a Certificate? 

Certificate Hierarchy: 
1. GeoTrust Global CA (Certificate of the whole internet) 
2. Google Internet Authority G2 
3. www.google.com : 
    Subject's Public key and Key Algorithm (RSA) Certificate binds the public key to the Google's identity

Identity misbinding: Binding between the identity and the public key.
(How can the other identity recover the inbox?)

Why trust the Certificate:
Issuer: I.e. Google Internet Authority G2
Certificate contains: 
1) ID of subject = google.com 
2) ID of issuer = Google Internet Authority 
3) Public key of the subject = pk ofgoogle.com 
4) Signature of the issuer (on the whole certificate) -> Algorithm used to sign it is SHA-256 With RSA Encryption. (Chrome bannedthe SHA-1 "secure" connection, and the collision was found Feb. 23, 2017) and The signature ensures the issuer can only produce the certificate

Hierarchy 2: 
1. ID of subject = Google Internet Authority 
2. ID issuer = GeoTrust 
3. Public key of subject = PK for Google Internet Authority 
4. Signature of issuer (signed with GeoTrust's private key)

On the top level: 
1. ID of subject = GeoTrust 
2. ID issuer = GeoTrust Global CA 
3. Public key of subject = PK for GeoTrust 
4. Signature from GeoTrust Global CA

Public key from the higher Hierarchy will allow us to verify the
signature of lower hierarchy. Where is the root of the trust? - Browser
comes with hard coded key (in ROOT), and the browser can trust GeoTrust
Global CA Number of Trusted Roots? - 40-ish…

If a child in the Hierarchy is compromised, the parent will revoke it,
(A signed message from parent says the certificate is invalid..)

Bank of America: Directly bought a key from the Root. (Symantec) Problem
with many roots: People can compromise any root, then can impersonate
any website.

If it is easy to find SHA-1 collision, people can forge fake signature
and pretend to be Google.

Sign_sk (m) = Sigma [Padding(H(m)]^d mod N

H() better not be SHA_1

Recall RSA Sk = (d, N), Pk = (e, N)


FLAME Attack: Code signing certificate (Attack on MD5) Code sigma signed
by the key.

The Flame attack forged the key and be able to update code to the
system. (Microsoft) Target Collision (Given x, find such that h(y) =
h(x))

HMAC cannot be used in RSA because RSA is a public key system, which is
not a symmetric key scheme.

DHZ Key exchange

Alice -------------------- Bob Group G generator g. (g is public)
Discrete log: Given g, y it is hard find x such that y = g^x

    1. Do no have anything share
    2. Alice:
        a. Choose secret x
        b. Compute g^x
        c. Alice send to g^x to Bob
    3. Bob:
        a.  will choose y, and compute g^y
        b. Key = (g^x)^y = g^(xy)
        c. Send g^y to Alice
    4. Alice:
        a. Key = (g^y)^x = g^(xy) 

(Secure for Passive Adversary, but not Active Adversary)

How to launch a identity misbinding to this: Since people can
impersonate Bob, anyone can tell Alice that he is BOB.
Alice --------- Eve 
How does Eve know how to be Bob? 
Alice ------ Eve ------- BOB

A ----> Let's use DHZ ---> B

B ---> Let's use DHZ ---> A

Then A has B's Pk_bob

B sends that: Bob, g^y, sign_sk(g^y, bob) ----In real world, B sends
the certificate chain, since A doesn't know B's pubic key. A verifies,
then sends that: Alice, g^x

One sided Authentication. B does not know who A is. Session key then is
used.
