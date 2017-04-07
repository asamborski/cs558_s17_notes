---
layout: notes
title: Weaknesses in the Internet
scribe: Ben Getchell
---

## 4/6 Lecture - Weaknesses in the Internet

### ISPs

End users (laptops, computers, phones, etc.) are connected to the internet via ISPs (Internet Service Providers). 
When traffic is sent from an end user within one network to an end user outside of that network, the traffic is 
routed along a path that is made up of ISP’s. 

Many different protocols are used in conjunction when traffic is sent over the internet:
  -	Protocol: In this context, a formalized kind of communication between systems. A protocol is typically made up of a precise set of messages that programs use to exchange information.
  -	TCP/IP for routing
      -	Data sent over the internet must be decomposed into smaller packets (and reassembled at the destination IP), 
      and there must be information included in each packet that indicates to each node along the path where the 
      packet is headed.
  -	BGP for route announcement
      -	Neighboring ISPs must be able to tell each other how many hops they are away from a particular destination IP.
  -	DNS for name/IP mapping.
      -	Names for websites (e.g. – google.com) must be mapped to the appropriate IP address.

### TCP Protocol Stack

The TCP stack is a set of layers of protocols, each of which make use of the functions of the layers beneath them. 
Since the flow of information is downward on the left-hand side and upwards on the right hand side, an important 
security concern is knowing what happens to data at lower levels of the stack. 

![](https://image.slidesharecdn.com/12-tcp-dns-140326164729-phpapp01/95/12-tcpdns-4-638.jpg?cb=1395852536)

Data is transported in discrete packets, each of which is structured in a precise way such that they can be used 
and interpreted by protocols further down the stack. Headers in packets indicate specific fields that can be used 
by various protocols, and the protocols themselves specify where certain information is located within a header. 
For example, the IP Header contains the source and destination IP’s. When a router receives a packet, it only 
looks at the IP header since it only cares about where to send the packet next.

![](http://www.potaroo.net/ispcol/2004-07/fig1.jpg)

As a packet travels down the TCP/IP stack, it’s header field is encapsulated by each layer it passes through. 
When a packet is received at it’s destination, different layers remove layers of encapsulation, revealing which 
layer to send the packet to next.

![](https://i.stack.imgur.com/CqG6z.gif)

Other relevant header fields:
  -	TTL: “Time to live”. This field is assigned an integer value, and at each node along a packet’s path, this 
  value is decremented. When the value of the TTL field reaches zero, a router will drop the packet. This field 
  exists because routing protocols are imperfect, and it could happen that a packet gets stuck in an infinite 
  loop between routers. 
  -	Fragmentation Offset: If a router is sending a packet to it’s next router (hop), and that next router can’t 
  handle packets of it’s current size, the packet must be fragmented into smaller packets. The fragmentation offset 
  indicates to the destination the order in which fragmented packets are supposed to be reassembled.

### IP Addresses and Routing

An IP address is a sequence of 32 bits, represented as four 8-bit integers (e.g. – 128.30.254.17). An ISP is 
identified with a subset of IP addresses that it owns. The subset of IP’s that an ISP owns is represented as an 
IP prefix, which has the following format:

	xxxxxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx/y

Where each x is a bit, and y is some integer representing which bits are fixed. For example, 

	128.25.128.0/24 

Means that the ISP with this prefix owns all IP addresses in range 128.25.128.0 to 128.25.128.255, since the first 
24 bits are fixed. In this case, this ISP would own 2^8 IP addresses.

The IP protocol is connectionless, which means that it offers no guarantee that any packets sent over it will actually 
reach their destination. It also does not guarantee that packets will be received in order at their destination. The IP 
protocol is designed in this way because it is concerned only with the transport of packets, and leaves other protocols 
to handle ordering and delivery guarantees. TCP, for example, is a connection-oriented protocol, which means that it 
guarantees that packets sent using TCP will both be delivered and will be delivered in order.

Each router stores a routing table which includes each destination IP prefix that it knows how to get to, and the next 
hop along the path that it knows about. The routing table doesn’t know the path itself (unless it is a single hop away); 
it only knows the next hop along the path, which is a neighboring router. Every router’s routing table is subject to 
change, as routers are eliminated or added and shortest paths get updated accordingly. 

Since there are “only” 2^32 possible IP addresses in the world, people have had to devise ways of making them less scarce. 
One such way is via Private Address Spaces, which effectively assign all IP addresses within a given subnet to a single IP 
address, concatenated with a port number. Thus, the destination IP for some IP within a subnet that uses private address 
spacing will look something like <IP_Address>:<Port_Number>. The router that traffic uses to access the subnet will convert 
the above address into the real IP address of the recipient when it receives a packet, and deliver it accordingly. 

### Security

Source IP’s are not authenticated, which means that end users are not required to populate the source IP field in each packet 
they send with their actual IP address. This presents security issues, especially in the context of DDoS attacks. For example, 
if somebody sent a ton of TCP requests to end users all over the internet with the source IP of someone they intended to 
attack, that source IP would be flooded with TCP SYN-ACKS, which are acknowledgement messages that TCP uses to open up a 
connection with an end user.

### User Datagram Protocol (UDP)

UDP is a connectionless protocol, since it doesn’t make use of acknowledgements upon packet delivery. Despite this, it is 
much faster than TCP (which is connection-oriented) since it has virtually zero overhead. Thus, UDP is primarily used in 
situations where latency matters more than reliability, e.g. – VoIP (Skype), video streaming, Network Time Protocol, etc.

### Transmission Control Protocol (TCP)

TCP is responsible for breaking data into discrete packets, and assigning them order numbers at the sender side. At the 
receiving end, TCP sends acknowledgements upon receiving any packet, indicating that it received a packet with order number 
n. TCP accounts for the fact that the IP protocol is connectionless, and so is unable to handle reliability concerns.
