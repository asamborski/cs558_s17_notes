---
layout: notes
title: BGP Security 
scribe: Seunghun Oh
---

# BGP Security
April 13th, 2017

## Subprefix Hijack

We can create a man-in-the-middle (MITM) situation by using what is called subprefix hijack.
Subprefix hijack “shaped” how traffic is communicated abroad in July 2013. The hijack takes
advantage of the fact that routers tend to choose the more specific IP address.

A “specific” IP address is one that tends to have less open address if it has a bigger prefix. A prefix
of an IP address is denoted by …/x, where x is an integer that denotes how many of the bits are
used to denote an address. This means the available number of reserved blocks is equivalent to
32 – x. If there are less blocks, the IP address is considered to be more specific.

A hijacker who wishes to perform a MITM eavesdropping can do so by inserting data with a more
specific prefix, overriding the victim’s IP address.

The man-in-the-middle must be careful with where he enters the IP address and AS data;
otherwise he may create a “black hole” of data. He can avoid a “black hole” by making sure
packets are sent to a victim’s receiving router.


## BGPSEC, RPKI, ROA

To prevent having a MITM situation, we can set up a Resource Public Key Infrastructure (RPKI) in
the domain routing with Route Origin Authentication (ROA). A regular domain structure is set up
as BGP. RPKI validates the origin of the data. For RPKI, crypto is done out of band, no changes are
made to BGP messages, and certifies IP prefix allocations.

Before RPKI is implemented, IP address can be forged; however, RPKI makes sure that the IP
address cannot be forged. This data is secured with a ROA to make sure data that is sent is
accurate. Note that if the exact IP address and AS data is used, then the path can be shortened
and data can be sent to the victim with RPKI verification.

However, this path shortening can be prevented by using a BGPSEC. BGPSEC builds on RPKI,
almost standardized, certifies announced routes, crypto is done online using public keys, and
major change is applied to BGP messages.

When RPKI is implemented to a BGPSEC, the IP address and AS is encrypted with RPKI. This means
both path shortening attacks as well as IP spoofing attacks no longer work. However, BGPSEC is
not a standard, because it costs more money than an unsecure path. In order to accommodate
both BGPSEC paths and BGP paths, downgrading must be possible. Thus, downgrading attacks
can occur. Network managers (when asked on a survey performed by Professor Goldberg)
preferred the cheaper and unsecure path over the secure path.
