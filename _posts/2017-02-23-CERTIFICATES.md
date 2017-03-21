---
layout: notes
title: "Certificates and DH key exchange."
scribe: Omar Sagga
---

**Identity miss-binding**: trick users to make people think your public key is someone else's key, so they encrypt messages using your key such that you would be able to decrypt their messages.
***
**Certificate structure**:
```
                          ------------------------
                          | ID of the subject    |
                          |----------------------|                      
                          | ID of the issuer     |
                          |----------------------|
    cert1 ------>         | Public_key of the    |
                          | subject.             |
                          |----------------------|
                          | Sign_{Secret key of  |
                          |  the issuer} (cert1) |
                          |----------------------|
```
+ ```
  signature "sigma" = [pad(HASH_FUNCTION(m))]^(d) mod N
  ```
+ We sign to ensure **only** the issuer can produce the message.
+ Each certificate could be signed using a different signing algorithm such that it gets specified in the certificate it self (could be SHA-256, ..).
***


**Certificate Hierarchy**:

Let's take **Google.com**'s certificate for example:

If we go bottom to top, we start with:

```
                          ---------------------------------------
                          | subject: Google.com                 |
                          |-------------------------------------|   
                          | issuer: Google Internet Authority   |
                          |-------------------------------------|
    Cert1 ------>         | Public_key of Google.com            |
                          |-------------------------------------|
                          | signature = Sign_{Secret key of     |
                          |  Google Internet Authority} (cert1) |
                          |-------------------------------------|
```
Then to verify that the signature of this certificate is correct we would need the
public key of **Google Internet Authority**, which we don't have. So Google.com has to also send this second certificate for the issuer's public key.

```
                          --------------------------------------------
                          | subject: Google Internet Authority       |
                          |------------------------------------------|   
                          | issuer: GeoTrust Inc.                    |
                          |------------------------------------------|
    Cert2 ------>         | Public key of Google Internet Authority  |
                          |------------------------------------------|
                          | signature = Sign_{Secret key of          |
                          |  GeoTrust Inc.} (cert2)                  |
                          |------------------------------------------|
```
Now that we have a certificate to verify **Google Internet Authority**'s we would face the same problem of how we can actually trust that GeoTrust signed this with their secret key, we don't have thier public key to check.

Now the way to get GeoTrust's public key, we would go to what is called **Trusted root directory**, it's a list of certificates that comes shipped with the browers and has some Trusted Authorities certificates, there we would find GeoTrust's certificate that has this structure:

```
                          --------------------------------
                          | subject: GeoTrust Inc.       |
                          |------------------------------|   
                          | issuer: GeoTrust Inc.        |
                          |------------------------------|
    Cert3 ------>         | Public key of GeoTrust Inc.  |
                          |------------------------------|
                          | signature = Sign_{Secret key |
                          |  of GeoTrust Inc.} (cert3)   |
                          |------------------------------|
```
+ This certificate is trusted and does not require any verification as it's already saved locally in the browser.

+ Each browser has its own directory of trusted roots.

+ Due to recent news about finding collisions in **SHA-1**, **SHA-1** cannot be used as a signing algorithm since it would be easy to forge signatures to verify fake certificates.
***


**Diffie Hellman Key Exchange**

This model is based on **Discrete Log Harness**

```
g comes from generator G and it's public
The following is easy:

           ____
x,g ----> |    | --> g^x
          |____|

The following is hard:

            ____
y   ---->  |    | --> x such that y = g^x
           |____|

```

Key exchange goes as follows:

```

-----                g^x                                    -----
| A |  -------------------------------------------------->  | B |
|   |                g^y                                    |   |
-----  <-------------------------------------------------   -----
+ A picks random x (secret)                                B picks y (secret)
Key = (g^y)^x = g^xy                                        Key = (g^x)^y = g^xy
```
+ Adversary knows g^x and g^y but
```
 (g^y)x(g^x) != g^xy
```
***


**One-sided Authentication**

```

-----                Lets use DHE                           -----
| A |  -------------------------------------------------->  | B |
|   |                Lets use DHE                           |   |
|   |  <-------------------------------------------------   -----
|   |     Bob (ID), g^y, Sign_bobSecretKey(g^y, Bob)        -----
|   |  <-------------------------------------------------   | B |
|   |                Alice (ID), g^x                        |   |
-----  ------------------------------------------------->   -----
+ A picks random x (secret)                                B picks y (secret)
Key = (g^y)^x = g^xy                                        Key = (g^x)^y = g^xy
```

+ Alice knows she's talking to Bob, but Bob does not know if he's actually talking to the real Alice or not.
