---
layout: notes
title: "Network Security Overview"
scribe: Chloe Fortuna
---

# The Internet: Layers, TCP, UDP, IP, IPSEC, DDoS Reflection Attacks, ARP

## TLS Packet
[![https://gyazo.com/7f750b4af0a56c17c022ac652614d121](https://i.gyazo.com/7f750b4af0a56c17c022ac652614d121.png)](https://gyazo.com/7f750b4af0a56c17c022ac652614d121)
The TLS packet contains 3 main layers:
- **IP Header:** Contains information for the IP layer. This section is not encrypted.
- **TCP layer:** Contains information for the TCP layer. This section is also not encrypted.
- **TLS layer**: Contains encrypted information, the MAC address, and padding.

### Metadata is still leaked in TLS
- Data that is not encrypted and is leaked:
    - Source and destination addresses
    - Ports
    - Protocol being used
    - Size of the packet
    - The number of packets
    - How often packets are being sent
        - We can tell how conversation's pattern looks. For example we can see who is talking more in a conversation.
        - Certain protocols always send packets at specific lengths.
- Leaked metadata has zero protection under the law. All metadata is exposed and the government can look at it.
- The actual conversation isn't leaked because it is encrypted, but information such as using Gmail is exposed.


TLS: end-to-end encryption from sender to receiver
TCP: controls the pipe connecting the sender to the receiver





## TCP (Transmission Control Protocol)
- In TCP, data is transferred through packets which arrive in order, and for each packet received, an acknowledgement is given.
- A handshake is performed in order to start the connection.
- **For example:** In order to transfer a movie, the movie is broken up into chunks. Each of the chunks becomes a packet such that each packet doesn't exceed the Maximum Transmission Unit.
- **Used when you want all of the information and it is okay to have delays.**
 
## UDP (User Datagram Protocol)
- UDP is an unreliable transport on top of IP. 
- There is no handshake because UDP is not connection-oriented.
- Useful for things like watching videos or talking on the phone, where no latency is more important than reliability. Lost packets are not sent again.
- The UDP header contains a checksum, which protects against random errors such as bit flipping.
    - However, the checksum is not secure because it is a public algorithm that is only 16 bits. As a result, it is easy to forge. 
- **Used when you want no delays and it is okay to lose a few packets.** 

  
## DoS Reflection and Amplification Attack (Denial of Service)
- An **off-path attack** that exploits that DNS is sent over UDP which is a simple protocol.
- The attacker sends a small query to a server, which sends a huge response to the victim. 
- Lots of large packets keep hitting the victim. Because the victim is hit with so much volume, he gets overwhelmed and crashes.
- Attacker can send the response to the victim through **source IP spoofing**: lying about the source IP
- **For example:** `monlist` is a small query, but gives you a big response of the last 600 people you've talked to

### Why can't we do this attack over TCP?
- You can't spoof a victim's IP over TCP
    - You need to perform start the TCP session through SYN, SYN-ACK, and ACK before sending the `monlist` query.
    - The message is going to be ignored because the ACK is not received. 
    
#### TCP Handshake
[![https://gyazo.com/ff9b096e7a555dc6a0f49bc3e0d88737](https://i.gyazo.com/ff9b096e7a555dc6a0f49bc3e0d88737.png)](https://gyazo.com/ff9b096e7a555dc6a0f49bc3e0d88737)

- The TCP packet contains two 32-bit random numbers:
    - sequence number
    - acknowledgement number
- We are unable to perform the attack because we don't know the random number that is being placed in into the sequence number at the start of the handshake.
- If you set up the connection with your IP, and then switch to the victim's IP, the connection will just be stopped
- Therefore, **random sequence numbers in TCP prevent off-path attacks**
- Cryptography is to prevent man-in-the-middle attacks.

### On-Path Attack for TCP
- An attacker reads traffic slowly, and if he sees something interesting, he takes action
- He can send a **reset packet** with the `reset` flag set, which closes a connection if the sequence/acknowledgement numbers are sensible
- **On-path model is very common for censorship**
- **For example:** Great Firewall of China
    - IP address blocking: dropping packets that go to a specific IP blocks the entire website
- Man-in-the-middle is more difficult for censorship because you have to be fast enough to stop packets 

## Link Layer
Computers have:
- **IP address:** assigned to you temporarily depending on what network you're on
- **MAC address:** permanent address

### ARP (Address Resolution Protocol)
A form of networking roll call:
- **ARP Request:** "Who has this IP address?" is broadcasted to everyone
- **ARP Reply:** A computer responses with: "I have that IP address. Here is my MAC address."
**For example:** Asking for a printer's IP address.

#### ARP Poisoning/Spoofing
- When the ARP request is broadcasted to everyone, anyone can answer.
- Spoofing is when you **claim you have the IP address when you don't**
- This can be used to become a man-in-the-middle
    - An attacker can pretend to be the access point 192.168.0.1, which is the router that lets us talk to the internet
- If you're spoofing a response from the router and both you and the router sends a response, there is a race condition:
    - If you are faster than the router, you win
    - Otherwise, the router's reponse will be accepted and not yours