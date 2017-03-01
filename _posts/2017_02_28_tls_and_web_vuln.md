---
layout: notes
title: "TLS and Web Vulnerabilities"
scribe: Eric Mooney
---

# News Presentation
## Yahoo Data Breach

### What happened
There are 3 main known data breaches since 2013, with the third just announced a few weeks ago.

Yahoo, up until 2013, had been storing passwords in MD5, unsalted, but since have been encrypting using bcrypt.

1. data breach #1 (~Aug 2013), over 1 billion accounts (with other userdata, including unsalted passwords) affected.
2. data breach #2 (~late 2014), 500 million accounts affected.
3. data breach #3 - breaches likely using "forged cookies".

### Possible attack vector for the 3rd data breach
HTTP Cookies allow the browser to have a stateful HTTP connection.

"XXS": [Cross-Site Scripting](https://en.wikipedia.org/wiki/Cross-site_scripting) attack
* Uses the website against its own users.
* Process: 
	* Attacker injects javascript into an un-sanitized textbox, etc. whose contents are later displayed on the website.
	* That javascript is then run in the browser and can be used to send a user's cookies to an attacker.
	* Attacker can then impersonate the user's client by using their cookies.

But was the attack conducted with forged or stolen cookies?
* If forged, attackers may have obtained access to Yahoo source code in order to generate valid cookies.
* If stolen, they were likely retreived using an XXS attack.

# Lecture Notes
## TLS Handshake
[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) is the web's most popular encrytion protocol, and is used to provide one-sided authentication.

Client is unknown to the server (the server uses an Application-level protocol such as a login page to identify the user). We need to establish session keys, and will use a protocol known as the "TLS Handshake".

*If the handshake fails, the browser will normally display "Connection Not Secure" or "Connection Refused".*

```
_______________________________________________________________________________________
 ________________________________                  ________________________________ 
|     Client "Your Browser"      |                |     Server "Facebook"          |
|--------------------------------|                |--------------------------------|
|root store: trusted public keys |                |  Public Key: (PK_F)            |
|   e.g. PK_Geotrust (PK_G_long) |                |  Secret Key: (SK_F)            |
|                                |                |  Cert(PK_F, subj: FB,          | 
|                                |                |           issuer: Geotrust)    |
|________________________________|                |________________________________|

   known to client:                                             known to server:
                           "Client Hello": ciphersuites, cr
        cr                   --------------------------------->>  ciphersuite, cr 

                           "Server Hello": ciphersuite, sr
    ciphersuite, sr    <<-----------------------------------         sr

                           g, g^a, Sign_SK_F(g, g^a, cr, sr)
                       <<----------------------------------        g, a, g^a

                          Cert(PK_F, FB, Geotrust) (detail below)
     g, g^a, PK_F      <<----------------------------------       

                                          g^b
       b, g^ab              --------------------------------->>   g^b, g^ba=g^ab
     ms, k_1, k_2                                                  ms, k_1, k_2

                             "Finished": MAC_ms(transcript1)
                            --------------------------------->>  verify_ms(transcript1)

                             "Finished": MAC_ms(transcript2)
  verify_ms(transcript2)  <<-------------------------------     

                               auth-enc_k_1(client_message)
      client_message        --------------------------------->>    client_message

                               auth-enc_k_2(server_message)
      server_message      <<-------------------------------        server_message  

_______________________________________________________________________________________
```

1. "Client Hello" generates a random value, client random "**cr**" and sends to server along with a list of their preferred **ciphersuite** (i.e. "I'd like to use SHA-256, and if that is not available, SHA-1").
	* A [ciphersuite](https://en.wikipedia.org/wiki/Cipher_suite) will include "a key exchange algorithm, a bulk encryption algorithm, a message authentication code (MAC) algorithm, and a pseudorandom function (PRF)"
2. "Server Hello" generates a random value, server random "**sr**" and sends to client along with the chosen **ciphersuite**. For this example, assume that the agreed upon ciphersuite key exchange was a **Diffie-Hellman** key exchange.
	* With DH, a chosen Group **G** with generator **g**
		* It is **Easy** to compute: g, x ---> g^x
		* It is **Hard** to compute: g, y ---> x such that g^x = y
			* Due to the hardness of the discrete log
3. The server chooses a generator **g** and a random **a**, and computes **g^a**, sending **g**, **g^a** and (**g, g^a, cr, and sr**) signed with the key of **SK_F**
4. The server also sends over its certificate Cert(PK_F), which for example, could be multi-layered as such:
```
 ________________________________ 
|     Certificate 2              |
|--------------------------------|
| Subject: Geotrust_short        |
| Public Key: PK_G_short         |   Why Sign_SK_G_long and Sign_SK_G_short?
| Issuer: Geotrust               |       - Sign_SK_G_short is only valid for a short period, 
|--------------------------------|         while Sign_SK_G_long may be valid for ~20 years.
| Sign_SK_G_long(cert2(Sub,PK,I))|       - If Sign_SK_G_short is stolen, it has less impact than
|________________________________|         if _long was stolen (_short can be revoked, _long would
                                           require global browser update).
               |
               |
               \/

 ________________________________ 
|     Certificate 1              |
|--------------------------------|
| Subject: Facebook              |
| Public Key: PK_F               |
| Issuer: Geotrust               |
|--------------------------------|
|Sign_SK_G_short(cert1(Sub,PK,I))|
|________________________________|

```
* The client then uses the root store and certificate chain to:
    * Verify cert2 with PK_G_long, obtain PK_G_short
    * Verify cert1 with PK_G_short, obtain PK_F
    * Verify Sign_SK_F(g, g^a, cr, sr) with PK_F, obtain **g**, **g^a**

5. Client chooses random **b**, computes **g^b** and sends to server. 
  * Both parties now have **g^ab**
  * Both sides then compute (ms, k_1, k_2) = PRF_g^ab(cr||sr)
    * ms = "master secret"
    * k_1 = session key for data sent from server to client
    * k_2 = session key for data sent from client to server
    * PRF_g^ab = Pseudo-random function (e.g. HMAC) keyed by g^ab 
6. The **ms** is used to key a "finished" message, which is the MAC(transcript1) which is all the messages of the key exchange to that point. It is sent to the server, who verifies the message using the ms it calculated.
7. The server then sends a return "finished" message, which is the MAC(transcript2), which is transcript1 + the client's "finished" message, keyed with ms.
8. Now, **k_1** and **k_2** are the session keys. The client can send an authenticated encryption message (e.g. GCM, AES_CBC-mode) to the server using k_1, and the server can do the same using k_2.

## Notes about the TLS Handshake
* Everything except the Cert(PK_F, subj: FB, issuer: Geotrust) is ephemeral. It is purchased from the issuer and stored in the server.

## Potential Security Weaknesses
* Because k_1, k_2 are short-lived, the government has attempted to implement a backdoor so as to be able to decrypt past messages that are unable to be decrypted after those keys are lost.
	* This is attempt known as the [Clipper Chip](http://www.nytimes.com/1994/06/12/magazine/battle-of-the-clipper-chip.html?pagewanted=all) protocol.
		* It was based on an algorithm called "Skipjack", but a professor found a bug in it (which helped lead to the demise of the government's attempt to implement it)
		* the goal was to create a "golden key" **k_g** that could decrypt ALL messages encrypted with the ephemeral k_1 or k_2 keys in the past. i.e.:
			* c = Enc_k_1(m)
			* Dec_k_1(c) = m
			* Dec_k_g(c) = m
	* If **k_g** existed, it would have to be used frequently. Combined with its extremely high value to attackers, it would be very likely to be obtained by unwanted parties.
	* The ephemeral nature of k_1 and k_2 in the DH key exchange (unlike the RSA key exchange) is actually a security benefit known as the "Forward secrecy" property. If an attacker records messages on the wire for a period of time, and then steals the SK_F, it could impersonate Facebook in the future and decrypt those messages, but it wouldn't be able to determine g^ab and thus not determine k_1, k_2. Those recorded messages would remain encrypted.
* The most valuable component of this protocol to an attacker is SK_G_long, as it would allow the attacker to impersonate any web server successfully.
