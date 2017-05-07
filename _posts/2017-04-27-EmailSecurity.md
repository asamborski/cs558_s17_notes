---
layout: notes
title: Email Security
scribe: Haydn Kennedy
---

# Logistics
* Reminder: Must disclose bugs to company before public (final project)
* 2 page Security News wrap-up report due may 3rd. Remeber to address Rudi Giuliani

# PGP - Pretty Good Privacy (for email)
* For encryption of email text
* Doesn't encrypt metadata (e.g. To, From, CC, time, subject)
* In order to read the text of the encrypted email, a password must be entered

## Encryption
* As we know, message length is a problem with RSA.
  * Solution: Use symmetric key (session keys) to encrypt the message, and use the reader's public key to encrypt the session key
* Messages are signed with the sender's secret key, and the reader can verify with the sender's public key
  * Acquiring/verifying public keys in PGP is often a convoluded processs. You need to find some way to verify with the person that the key you have is theres.
* There is a PGP key server where keys are stored and matched with their owners
  * Not a thorough/efficient process, and doesn't guarantee people are who they say they are

* Remember: even if PGP works, the subject and metadata are not encrypted
  * This is still a large security risk
* However, most people don't even use PGP

# SMTP (Simple Mail Transfer Protocol)
* SMTP - 
  1. user sends an email to the server (a Mail Transfer Agent)
  2. MTAs exchange mail
  3. MTA sends mail to receiving user
* Users/clients speak non-standard protocols to their MTA

* We will focus on connection between mail servers
* Mail servers use DNS to discover eachother
  * Query for MX Record (mail transfer) given web-address, and a mail server host name is returned
  * DNS A Record is returned for mail server host name
* Fraudulent DNS responses
  * A study examining DNS servers found hundreds of mail servers that were accepting mail not addressed to them (purposely intercepting mail)

## Security of email over SMTP
* SMTP can be run in encrypted mode, or unencrypted mode
* TLS retrofitted onto SMTP
  * STARTTLS added into SMTP vocabulary (tells other server you are interested in using encryption)
  * Everything sent between MTAs is encrypted when using SMTP w/ TLS
  * **Note:** encryption must be agreed upon by both sides. Otherwise, everything is unencrypted (a result of allowing deprecated security practices for compatibility)
* MTAs use two-sided authentication to guarantee the other MTA is who they say they are
  * This is still vulnerable to MITM attacks (can intercept or drop STARTTLS packet)
    * This is called a Downgrade Attack (new versions must work with old versions, so failure of new features is ignored)

* STARTTLS Downgrade attack was found happening in Cisco email security devices sitting between mail servers which stopped encryption to allow for spam filtering
* A study was done indicating that most MTAs speaking TLS are not checking validity of certificates

* However, there are products being developed to increase email security:
  * SPF, DKIM, DMARC -> allow you to authenticate source server
* DKIM - allows you to verify a signature through a DKIM query to a DNS server (if that server is secure)
* DMARC - dictates policy we use with DKIM
  * e.g. "If my signatures don't validate, reject my emails."
  
* Email is built way better for reliability than security
