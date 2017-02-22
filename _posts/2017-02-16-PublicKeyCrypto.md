---
layout: notes
title: "Public Key Cryptography"
scribe: Clare Bornstein
---

Symmetric Crypto: Alice and Bob decide on a key to encrypt a message when one wants to send a message to the other- what happens when Alice wants to send messages to many different people? 
* Impossible to store enough secret keys to encrypt messages between every pair of entities in the world

Public Key Crypto: Facilitates broad communication needs as opposed to symmetric (pair) cryptography

MAC vs. Digital Signature

MAC: Signed so only Bob can verify; 
CCA2 Security: Pr[VerK(m*,  t*) = 1] = 1/2L where L is the length of m

```
Ak     --- m, t --->   Bk
m		                 accept if:
t = MACk(m)	        Verk(m,t) =1
```

Digital Signature: PKA, SKA Signed so anyone can verify; 
CCA2 Security: Pr[VerK(m*,  σ*) = 1] = 1/2L where L is the length of m

```
ASKA     --- m, σ--->   B
m		                accept if:
σ= SigSKA(m)	    VerPKA(m, σ) =1
```

Correctness: VerPK(m, SigSK(m))=1
Example: A DNS server signs IP address returns with its unique server signature to prove authenticity and that the user is actually directed to the site they intended to go to; however, public key distribution is a widespread problem. A signature can be valid, but come from an impersonator.

Public Key Encryption Scheme: Not a MAC/signature- provides confidentiality, but not authenticity. Combine with Digital Signature above to provide authenticity.

```
A PKB     ---    c   --->   BSKB		
      c= EncPKB(m)	DecSKB(c) =m
```

Encrypted with public key of recipient, and decrypted with secret key of recipient. 
* Scheme guarantees that only the recipient can decrypt with secret key- no secrecy if a message can be decrypted with a public key!
* Note that above scheme provides no measure of authenticity on its own, because Alice uses Bob’s public key to encrypt
Building a Public Key Crypto System: RSA
RSA is comprised of:
* 2 very large 2048-bit primes p and q
* p and q are secret to everyone in the RSA “black box”; p*q=N
* N: RSA Modulus
* e: encryption exponent (often 3) 
Easy: find me mod N when given (e, N, m)
Hard: find m such that y = me mod N; the “eth” root of y
Easy: known decryption exponent: d = e-1 mod ϕ(N) where ϕ(N) = (p-1)(q-1)
	yd mod N = m, where m such that y = me mod N
Construction:
```
A PKB     ---    y   --->   BSKB		
y= EncPKB(m)	        Dec(y) =yd mod N
```
Textbook RSA: EncPKB(m) = me mod N
Actual RSA: EncPKB(m) = [Pad(m)]e mod N, where Pad is random

* Each time RSA is run, p and q must be fresh—a new modulus each time
  * Random: p, q
  * Fixed: e
  * Public Key: N, e
  * Private Key: N, d
* If assume RSA is secure, we are assuming that factorization of large primes is hard

Key Generation: 
RSAGen() = p,q,e
d = e-1 mod (p-1)(q-1)
Keep Secret: d, p, q
Make Public: e, N


* [Notes 1]({{ site.url }}/notes/2-16-17.pdf)
* [Notes 2]({{ site.url }}/notes/2-16-17-2.pdf)
