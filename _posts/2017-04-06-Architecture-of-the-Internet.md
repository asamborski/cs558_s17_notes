---
layout: notes
title: Architecture of the Internet
scribe: Calvin Liang
---

# Architecture of the Internet (April 6, 2017 Lecture with Professor Crovella)

**Internet Infrastructure**

* The physical infrastructure a network consists of two hosts where one host is sending data to another who is receiving (aka an end host) through the use of intermediate devices (ie. routers)
* Anybody can be an ISP
* Key components of the Internet:
  * Domain Name System
    * Domain names like cs.bu.edu are converted into IP addresses
  * The ability to find routes from one end of the Internet to another (ie. between hosts)
      * TCP/IP used for routing and messaging
      * BGP used for routing announcements (ie. exchanges of routing and reachability information between routers)
      
**TCP**

* Breakup of TCP Protocol Stack into subsystems (ie. layers):

```
+----------------+                                             +----------------+ 
|                |             Application Protocol            |                | 
|  Application   |   <------------------------------------>    |  Application   |
|                |                                             |                | 
+----------------+                                             +----------------+ 
|                |                 TCP Protocol                |                | 
|   Transport    |   <------------------------------------>    |   Transport    |     Port #
|                |                                             |                | 
+----------------+                +-----------+                +----------------+  
|                |   IP Protocol  |           |  IP Protocol   |                | 
|    Network     |  <---------->  |    IP     |  <---------->  |    Network     |     IP Address
|                |                |           |                |                | 
+----------------+                +-----------+                +----------------+  
|                |      Data      |           |      Data      |                | 
|      Link      |  <---------->  |  Network  |  <---------->  |      Link      |     MAC Address
|                |      Link      |  Access   |      Link      |                | 
+----------------+                +-----------+                +----------------+  
```

* This demonstrates the communication process between corresponding layers on different systems
* The flow of control/information in a network operates from top to bottom
* A protocol is defined as a "precise set of messages exchanged to accomplish some purpose"

**Data Formats**
```                              (TCP Header)
+----------------+                      |     +----------------------------------+
|                |                      |     |    Application message - data    |
|   Application  |   message            |     +----------------------------------+
|                |                      |        |              |              |
+----------------+                      V        V              V              V
|                |                    +-----+------+     +-----+------+     +-----+------+
|    Transport   |   segment          | TCP | data |     | TCP | data |     | TCP | data |
|                |                    +-----+------+     +-----+------+     +-----+------+
+----------------+                                             |              |
|                |                                             V              V
|  Network (IP)  |   packet                             +------+-------+------+
|                |              (TCP encapsulated --->  |  IP  |  TCP  | data |
+----------------+                in an IP header)      +------+-------+------+
|                |                                             |              |
|   Link Layer   |   frame                                     V              V
|                |                                +-----+------+-------+------+-----+
+----------------+                                | ETH |  IP  |  TCP  | data | ETF |
                                                  +-----+------+-------+------+-----+
                                                     ^                           ^
                                                     |                           |
                                               Link (Ethernet)              Link (Ethernet)
                                                   Header                      Trailer                               
```

* Data is broken up into packets before being exchanged over the internet
  * The way packets are turned into actual protocols is through the use of headers. This is done through encapsulation
    * Example: TCP being encapsulated into IP is the same as saying a TCP packet is put into an IP packet
* TCP segments become packets at the IP layer, the result of which becomes messages at the Link layer
* TCP contains "ports" which are designators of an application
  * Two in particular are the source port and destination port
* A client chooses the source port so when communication is sent back to him/her, the client's system will know which application to deliver the message to

**IP Prefixes & Address**
* IP Addresses are 32 bits in length (ie. it has a size of 2^32 = 4 billion)
* IP Addresses are broken into four 8-bit segments (aka octets) to make it more convenient for writing down
* Prefixes are a collection of addresses that all have the same initial bits (denoted by a /# after the IP address)
  * Example: 204.16.254.0/24
    * 204.16.254 is the prefix
    * /24 indicates that the organization owns a total of 2^8 = 256 addresses (ie. all the addresses between 204.16.254.0 and 204.16.254.255)
  
**Steps in IP Routing**
1) Say Meg wants to send data to Tom...
2) An IP packet containing Meg's source port and a destination port is sent through a series of routers
3) A router has a routing/forwarding table that computes a "Next Hop IP" for the destination IP address in the packet using the packet header. It then forwards the packet accordingly to the next router and this process is repeated - as a typical route uses several hops - until the packet reaches the destination address

* Packets are sent independently, so they can take entirely different paths (ie. routers can use different Next Hops) and can arrive at different times as well.

**IP Protocol Functions**
* Routing:
  * Recall: routers compute paths from a source to a destination
  * Hosts only need to know where a router/gateway is (ie. one that is connected to a network)
    * Another way to say this is that an end host has a default gateway that the host can send all of his/her packets to
  * Routers, which have multiple connections to other routers, need to know what the right Next Hop is for all destinations on the internet
* Fragmentation and Reassembly:
  * When a router wants to send a packet to a Next Hop and knows the Next Hop can't handle a packet that large in data size, it can send the Internet Protocol packets as fragments, where they are reassembled back into a whole after arriving at the destination
* Error Reporting:
  * Internet Control Message Protocol (ICMP) allows packets to be dropped within a network
    * With how the network is designed, there is no guarantee that a packet will always make it to its destination
    * As a result, if a router decides that it's too busy, it can freely drop any packet that it wants
    * A router can also send a packet back to a source if the packet is dropped, but this is optional
* TTL Field:
  * TTL stands for "Time To Live"
  * How it works:
    * When a packet is first created, we attach an integer number to it. Every time a router receives this packet and passes it on (ie. after every hop), the number is decremented by 1
    * When a router receives a packet with a TTL of 0, it drops the packet. This prevents the network from being congested with endlessly cycling packets.
    
**NATS - Network Address Translation**
* This is the idea that we can take a bunch of hosts within an organization and give them "private" addresses from a "specially allocated space" (private address space)
  * When data is sent outside of this organization through their gateway to the internet, the hosts' addresses will be changed to the router's address followed by a port
    * Example: Host A (w/ IP Address: 10.0.0.1) and Host B (w/ IP Address: 10.0.0.2) both send data to a router (w/ IP Address: 123.1.1.1). The router changes both their IP Addresses to 123.1.1.1:1000 and 123.1.1.1:2000, respectively, before sending the data out to the internet
   * Different ports will always be given to different hosts
   * These addresses are never used outside of the organization's gateway
   * Only works for TCP/UDP connections that have ports

**UDP - User Datagram Protocol**
* Very simple, but unreliable form of Transport on top of the IP layer
  * The UDP Header consists of only two ports (source & destination), a UDP length, and a UDP cksum
* UDP transport connection to another host provides no guarantee that the data (ie. every packet) you send will make it to the destination
  * "You don't have to get the data there; just do your best :)"
  * Used for VoIP, video, NTP (network time protocol), etc. where latency matters more than reliability
    * Delivers to the application as soon as the data arrives at the network interface
    * For Skype, it's important for your voice to be synchronized with the video so you can have a conversation.
* Dangers
  * There is no authentication of source IP addresses 
    * Clients are trusted to embed correct source IP, but there is no actual requirement for this
    * An individual can put essentially anything inside the source field
* Attacks
   * An attacker could potentially open up a connection to another host and put any source address onto the host's packets so when the destination sends a response back, that response will instead be sent to the forged source IP.
  * Anonymous DoS attacks
    * An attacker may send a large quantity of packets to all different hosts on the internet, with each packet containing a targeted victim's address as the source. The hosts will all reply back to that victim, which sends the victim an enormous flood of packets
  * Anonymous infection attacks (e.g. slammer worm)
    * An attacker can craft a malicious packet and send it to a host (while using a random source address to hide) that will infect the host's system upon receiving the packet


**TCP - Transmission Control Protocol**
* More reliable than UDP because it is connection-oriented and it preserves order
  * The sender breaks the data up into packets, all of which are assigned a number to indicate an order
    * Packets contain two sequence numbers - one specifying where the packet is in the stream, and one that signifies the number of bytes received
  * The receiver acknowledges receipt of the packets and reassembles the packets in correct order. If no acknowledgement has been made after a certain amount of time, the packets are then resent until there is acknowledgement.
  * Process of establishing a connection before sending data:
    * Device B opens a connection by sending a SYN packet to Device A
    * If Device A is willing to open a connection with Device B, it will receive the SYN packet and send a SYN-ACK packet back.
    * After receiving the SYN-ACK packet, Device B will send an ACK packet, and after Device A receives it will they know the connection is open and working.
  
* Security Problems
  * Network packets pass by untrusted hosts
    * This allows eavesdropping and packet sniffing (ie. collecting information, such as passwords, from the packets)
    * This is especially easy when an attacker has control of a machine that is close to the victim
  * If sequence numbers aren't random, an attacker can easily guess them and perform attacks such as spoofing or session hijacking
