---
layout: notes
title: Internet Weaknesses 
scribe: Tyrone Hou
---

# April 6th notes

## Internet weaknesses

### Networking Infrastructure

Host - [ISP] - [ISP] - Host

-Anyone can be an ISP

-local and interdomain routing
- TCP/IP for routing/messaging
- BGP for touting announcements

### Domain Name System
-Find IP address from symbolic name

### Layers of the internet
- Built on a hierachichal set of protocols 
- Think as a correspondence between specific layers

### TCP Protocol Stack
Application - Application
TCP - Transport Port #
IP - Network IP addr
Link - Network Link MAC address

What does data look like at each layer?
App: app data

broken into

TCP: TCP|segment
IP: IP|packets
Link: frame ETH|IP|TCP|data|ET

What does a TCP header contain?
32 bits * 7
Source port | dest port
seq num
ack num
data offset window
checksum | urgent pointer
options | padding
Data

### IP Addressing
32 bits total
4 8 bit segments (uint rep)
prefix: set of addresses w/ same initial x bits

### How does routing work?
Say Meg wants to send a msg to Tom.
She sends a packet with src and dst ip to her router.
Each router has a forwarding table
For each destination prefix, it has a next hop ip;
the next router to send a packet to on its path

Packets can take different paths, and arrive out of order
no ordering/delivery route guarantee.

NATS = Private address space

How can we still be adding computers to the network?
- Have 'local' addresses that router NATS to global IP space

### User Datagram Protocol
unreliable
- No acks/ congestion control
- Used for VoIP, video, NTP
- lactency matters more than reliability

UDP header [src port | dst port] (32 bits)
		   [length   |   cksum ]

Problem: No src IP auth
Burden on client to embed correct source IP

### TCP
Connection oriented, preservers order
If you know the sequence number, you can insert packets into the connection. The random seq nonce used in the initial Handshake helps protect against this.

Vulnerable to flood attacks.
