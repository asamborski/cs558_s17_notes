---
layout: notes
title: "April 11, 2017: Internet Layers; TCP, UDP, IP, IPSEC, ARP, DDos Reflection Attack"
scribe: Sailung Yeung
---
# Lecture: April 11, 2017
## Group Presentation : none

## Lecture: Network Layers (TCP, UDP, IP, IPSEC, ARP)

### TCP & TLS
![alt tag](http://www.cisco.com/c/dam/en_us/about/ac123/ac147/downloads/customer/internetprotocoljournal/ipj_3-2/images/figure01.gif)


```
TCP/IP packet
IP header --> IP layer
TCP header --> TCP layer
```

- Allow packet arrive in order. Every sent packet give a acknowledge. (done in TCP header)
- TLS packet relatively ugly, because only TLS layer encrypted (with MAC). Not encrypting higher layers (IP layer and TCP layer).


- Q: maximum lenght of data should be included?
- 1500 bytes(has to make sure not too long) MTU(maximum trans unit).
**eg. chop a movie into block and send them. MAC the movie seperatly.**

- Q: when dividing the packet use what kind of block cipher? 
- Depends on the MAC scheme.

- Information might be leaking (TLS): ( by using traffic analysis / metadata lawyer aspect)
  1. IP addresses (google, medical website)
  2. Port and type of portocal speaking
  3. Number of packet and the size of individual packet
  4. See the timming inbetween the packets arrival **e.g. chatting app, how long talking, who is talking more**
  __certain protocals will have certain packet at certain length. (laws do not protect the metadata)__

- Q: is it illegal for evey one to look at the metadata? 
- Not sure (but comcast is always doing it)

- However, the information of http page will not leak. Eventhough the website browsing information will be leaked.
- DNS also worth protecting.

### IPSEC vs TLS
![alt tag](https://i.stack.imgur.com/iBe08.png)

- IP: telling router where to pass the packet next (link by link)
- TCP: controlling the flow from sender to the routers.
- TLS: end to end thing. from the client to the server.
- IPSEC: 
  1. mode 1 encrypt all the individual pip between two router. 
  2. mode 2 one router encrypt everything to another router.


### UDP

- one way of doing transfer in network
- only gives source ip, destination ip, source port, checksum(checksum is not secure and easy to get forged)
- no ordering, no acknowledgement.

![alt tag](http://obaida.info/blog/wp-content/uploads/2016/01/TCP_UDP_headers.jpg)

- if delays are ok then use TCP.
- if rather lose packet but no delay then UDP.

### DDoS(denial of service) Reflection & Amplification Attack (Source IP spoofing)
![alt tag](https://i.ytimg.com/vi/xTKjHWkDwP0/maxresdefault.jpg)

- Attacker send a short DNS query with source IP of victum to some public DNS server
- DNS Huge response to victum
- massive messages get directed to victim --> victim will go down

- vualnerbility: 
  1. DNS server will responed to every query. 
  2. UDP can be sent to every one.

- Portaction: 
  1. detecting weird traffic.
  2. use other differenct network services add more protection.

- Q:why cannot send over TCP?
- TCP cannot lie about the source IP, becuase the syn-ack protocal.
- Also cannot fake ACK from victum, becuase of Nounce. **sequnce number and acknowledgement number are 32 bits --> 1/2^64 probaility that the attack will work**

- Q:can get someone else black listed by Monelist?
- It is possible.

- Q:why do not use hand shake in UDP? 
- Too much latency.

- Q:if set up the TCP connection then spoof IP? 
- Will not work, since TCP will drop the packet.

- Q:if man in the middle for TCP? 
- Will succeed in injecting all the packets the attacker want.

- __If TLS is done inproperly then the security will break if key stolen.__

### On-path-attack for TCP
- Constently lisening to the TCP connection and is too slow to drop any packet. But can send packet before the connection ends.
- Random sequence number in TCP prevents off-path attack, but do not prevent on-path-attack.
- sensorship systems will be exmaples for on-path-attack

```
Great fire wall of China (a sensorship system):
Constently listen to the TCP connection unwrap the http page(payload) and look for the censored key-words(deeppacket inspection).
If something goes wrong, then close the TCP connection by sending both sides a packet with RST flag.
so sender and reciever will not have the connection again

```


### ARP
- Broadcasting the MAC address and the IP address map.
- make a local area connect by sending a broadcasting request.

*e.g. asking for printer IP address, then response will have ack.*

- ARP spoof: 
  1. attacker can spoof the IP address with its own MAC address. 
  2. setting up a man in the middle attack. 
  3. will not alwasy success**protected by law !!!!!!**


### Eather net(link layer technology)
- MAC address is individual to the physical machine
*e.g. plug a eather net cable, personal device to the wifi point*

- MAC address will not change( IP might be changing.)
- So each machine has a map of MAC address with IP address.
