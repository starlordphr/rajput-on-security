---
layout: post
title: Distributed Denial of Service Attacks - An Overview
tags: [Scientific Research in Cyber Security]
---

Denial of service (DoS) is characterized by an explicit attempt by the attacker to prevent a legitimate user from accessing or using a particular resource. The distributed aspect of these DoS attacks makes it difficult to be prevented as a lot of compromised host machines are used for performing these attacks. Such a distributed attempt is known as distributed denial of service (DDoS) \[[1]\].

## General Approach
DDoS attacks involve a victim, i.e the target of the attack. Second, it involves an attack agent that is responsible for performing the attack on the target victim. These agents are deployed by the attacker after compromising a host machine. The third component of a DDoS attack is the control master program, which coordinates the attack. Finally, the attacker uses the control program to launch the attack.

![General-DDoS](/assets/img/posts/ScientificResearch/General-DDoS.png)

---

## Methods of DoS
In this section we will talk about some of the techniques that are used for performing denial of service.

### Smurf Attack
This is a network layer DDoS attack appropriately named after the *DDOS.Smurf* malware. This attack uses ICMP Echo message for overwhelming the target's network resources.

During normal operation, a host A sends an ICMP Echo (ping) packet to host B, it automatically sends back a reply to host. But, Smurf attack exploits broadcast networks for amplifying the attack traffic.

The attack be broken down into following steps:
1. Smurf malware is used to generate a fake Echo Request packet with the spoofed IP address of the target victim.
2. This packet is then sent to an intermediate broadcast network.
3. The broadcast network replies to this request such that all the individual hosts in the network reply to the source IP address, which has been spoofed to the target victim.
4. This leads to a huge amount of incoming ICMP packets from the broadcast network which consumes the victim's bandwidth, hence bringing it down.

To give you the scale of the amplification, if the attacker uses a broadcast network of 1000 servers, a single ICMP Echo Request leads to 1000 replies. It is worth noting that in this attack, along with the victim, the intermediate network will be degraded as well \[[2]\].

![Smurf](/assets/img/posts/ScientificResearch/Smurf.png)

### TCP SYN Flood Attack
TCP SYN flood attack exploits the three way handshake used by TCP protocol for establishing connection between two entities. Typical three way handshakes go as follows:
1. Client initiates connection to the server by sending SYN (Synchronize) packet.
2. The server replies to the client by sending SYN-ACK (Synchronize-Acknowledge) and creates a new connection in the limited size buffer for open connections.
3. The client replies to the server with an ACK packet and the connection is established.

![TCP-SYN-cropped](/assets/img/posts/ScientificResearch/TCP-SYN-cropped.png)

Consider the situation where a client sends the SYN packet but never replies to the SYN-ACK packet. In this case the server will have a half open connection with the client until the timeout occurs. This particular behavior is exploited by the attacker. If the adversary can have multiple compromised machines make half open connections with the server and keep them open, then legitimate users will not be able to connect to the server \[[3]\].

### UDP Flood Attack
User Datagram Protocol (UDP) is a sessionless and stateless networking protocol which does not require a three way handshake. Moreover, it has low overhead and the UDP traffic does not need to be checked.

For performing this attack, the adversary can send UDP packets to all the ports of the target. On receiving this packet the server checks for any service listening on the port and if none is found, then an appropriate reply is sent back to the client. But, if these messages are sent on all the ports and in bulk, the server resources will be overwhelmed and legitimate users will not be able to access any service on the server \[[4]\].

---

## Methods of DDoS
In this section we will talk about scaling DoS attacks:

### Trinoo
Trinoo was the first well known DDoS attack against University of Minnesota using UDP flood attack. This attack was done over a period of two days where the attacker did not spoof the source address for the compromised machines. Hence, these machine operators were informed about the ongoing attack and the attacker responded to this by just adding new agents.

This attack relied on compromising Solaris 2.X systems where the attack introduced itself by exploiting buffer over-run bug in the Remote Procedure Call (RPC) service 'statd', 'cmsd' and 'ttdbserverd'. The attacker begins by compromising one of many master machines which are loaded with rootkits, vulnerability scanning tool and a list of vulnerable hosts. Master scans all the possible vulnerable hosts and passes a list to the exploit script which compromises each of these hosts. Then a remote shell is setup on the compromised host listening on TCP/1524 and creates a new list of 'owned' hosts. This list is then passed to another script that sets up a rootkit on the owned machines completing the Trinoo network. This network can implement UDP Flood attacks against the target victim \[[5]\].

![Trinoo](/assets/img/posts/ScientificResearch/Trinoo.png)

The attack begins when the adversary connects to the master using Telnet to the TCP port 27665 and enters the password. Masters then pass commands to trinoo daemons via UDP port 27444. These commands are password protected and are of the form: arg1 password arg2. Daemons respond to masters on UDP port 31335.

Some of the commands that could be passed are as follows:
1. quit - To logoff from the master
2. dos IP - To perform DDoS attack on the IP mentioned in the instruction.
3. mdos - To launch multiple DDoS attacks.
4. aaa password IP - DoS attack IP by sending UDP packets to random ports.
5. dle - Shutdown daemon.

### Tribe Flood Network (TFN)
Both the client and daemon for TFN are compiled and run on Red Hat Linux 6.0. Whereas the daemon has been seen to run on above stated vulnerable 2.X version of Solaris operating system.

The TFN network is made up of tribe client program "tribe.c" and tribe daemon program "td.c", which can be represented by the figure shown below.

![Tribe-Flood](/assets/img/posts/ScientificResearch/Tribe-Flood.png)

In this network, the adversary controls one or more client, each of which can control many daemons. These daemons are instructed to coordinate a massive attack against a target.

Remote control in the TFN network is obtained by a command line execution of the client program which can be done via various methods such as remote shell bound to a TCP port, UDP based client/server shells, ICMP based client/server shells, etc. Communication from the client to daemons is accomplished by ICMP ECHO Reply packets. There is no TCP or UDP based communication between the client and the daemons, this helps the TFN network to stay hidden from some network monitoring tools. This is because these tools do not show the data section of ICMP packets or some times do not parse all the ICMP type specific fields and hence making the communication between client and the daemon difficult to monitor \[[6]\].

While the client is not password protected, each command sent to the deamon is a 16 bit binary number in the id field of the ICMP ECHO Reply packet. The sequence number for this communication is a constant 0x0000, which makes it look like an initial reply to a ping command. The values for the command are generally defined in config.h file and the default values in the file are shown below.

```
#ifndef _CONFIG_H

/* user defined values for the teletubby flood network */

#define HIDEME "tfn-daemon"
#define HIDEKIDS "tfn-child"
#define CHLD_MAX 50

/* #define ATTACKLOG "attack.log" keep a log of attacks/victims on all
   hosts running td for debugging etc. (hint: bad idea) */

/* These are like passwords, you might want to change them */

#define ID_ACK		123	/* for replies to the client */
#define ID_SHELL	456	/* to bind a rootshell, optional */
#define ID_PSIZE	789	/* to change size of udp/icmp packets */
#define ID_SWITCH	234	/* to switch spoofing mode */
#define ID_STOPIT	567	/* to stop flooding */
#define ID_SENDUDP	890	/* to udp flood */
#define ID_SENDSYN	345	/* to syn flood */
#define ID_SYNPORT	678	/* to set port */
#define ID_ICMP		901	/* to icmp flood */
#define ID_SMURF	666	/* haps! haps! */

#define _CONFIG_H
#endif
-------
```

TFN can implement Smurf, SYN Flood, UDP Flood, and ICMP Flood attacks.

### Stacheldraht (German term for "barbed wire")
Stacheldraht network consists of some handler programs "mserv.c" and a large number of agents "client.c" connected to each of these handler programs. The network would look something like this:

![Stacheldraht](/assets/img/posts/ScientificResearch/Stacheldraht.png)

The attacker controls one or more handlers using encrypting clients, which inturn control many agents. However the maximum allowed agents per handler is restricted to a maximum of 1000. This is to ensure that the number of open file handlers per process do not reach the 1024 limit.

Remote control of Stacheldraht network is done by a simple client that uses symmetric key encryption for communication between the handler and itself on TCP port 16660. The client takes a simgle argument which is the address of the handler and tries to establish communication with it. On the other hand, communication between handler and agent is done on TCP 65000 and the replies from agent are given as ICMP ECHO packets. Stacheldraht can implement Smurf, SYN Flood, UDP Flood, and ICMP Flood attacks \[[7]\].

Some of the commands supported by Stacheldraht are shown below:
1. .killall : Kills all active agent
2. .madd ip1\[:ip2\[:ipN\]\] : Add IP addresses to the list of attack victims
3. .mdie : Send die request to all agents
4. .mdos : Begin DoS attack
5. .micmp ip1\[:ip2\[:ipN\]\] : Begin ICMP flood attack against specified IP addresses
6. .msyn ip1\[:ip2\[:ipN\]\] : Begin TCP SYN flood attack on specified IP addresses.
7. .showalive : Show all live agents
8. .showdead : Show all dead agents

### Shaft Network
Modeled after Trinoo, Shaft network is made up of one or more handler programs "Shaftmaster" and a large set of agents "shaftnode". The adversary uses a telnet like program to connect to the handler on the TCP port 20432. One of the major differences between Trinoo and Shaft is that this network has the ability to switch handler servers and handler ports on the fly so that detection via an Intrusion Detection System (IDS) can be avoided \[[8]\].

Some of the commands used in the Shaft network are as follows:
1. size \<size\> : Generates a flood packet of "size"
2. type <0|1|2|3> : Type of DoS to run (0 UDP, 1 TCP, 2 UDP/TCP/ICMP, 3 ICMP)
3. time \<length\> : Length of DoS attack in seconds
4. own \<victim\> : Add victim to the list of target hosts.

### TFN2K Network
TFN2K network uses all the three TCP, UDP and ICMP to communicate between the control master program and the attack daemons. Packet headers between master and agent are randomized with the excpetion of ICMP which always uses the header corresponding to ICMP ECHO reply. Unlike other networks, the daemons are completely silent so avoid detection and do no acknowledge the commands received by it. So often commands have to be sent multiple times. All the commands are encrypted using a key-based CAST-256 algorithm. TFN2K can implement Smurf, SYN, UDP, and ICMP Flood attacks \[[9]\].

---

## Conclusion
So, in this post we discussed a little bit about the inner working of the attack and the corresponding networks that are used to perform DDoS attacks.

---

## References
\[1\] Lau, Felix & H. Rubin, Stuart & Smith, M.H. & Trajkovic, Ljiljana. (2000). "Distributed denial of service attacks".
\[2\] Imperva Incapsula, "SMURF DDOS ATTACK".
\[3\] Imperva Incapsula, "TCP SYN FLOOD".
\[4\] Imperva Incapsula, "UDP FLOOD".
\[5\] Dittrich D., "The DoS Project's trinoo distributed denial of service attack tool".
\[6\] Dittrich D., "The Tribe Flood Network distributed denial of service attack tool".
\[7\] Dittrich D., "The stacheldraht distributed denial of service attack tool".
\[8\] Dietrich S., "An analysis of the Shaft distributed denial of service tool".
\[9\] Barlow J., Thrower W., "TFN2K - An Analysis (Revision : 1.3)".


[1]: https://www.researchgate.net/publication/3876432_Distributed_denial_of_service_attacks "Distributed Denial of Service Attacks"
[2]: https://www.incapsula.com/ddos/attack-glossary/smurf-attack-ddos.html "SMURF DDOS ATTACK"
[3]: https://www.incapsula.com/ddos/attack-glossary/syn-flood.html "TCP SYN FLOOD"
[4]: https://www.incapsula.com/ddos/attack-glossary/udp-flood.html "UDP FLOOD"
[5]: https://staff.washington.edu/dittrich/misc/trinoo.analysis.txt "The DoS Project's \"trinoo\" distributed denial of service attack tool"
[6]: https://staff.washington.edu/dittrich/misc/tfn.analysis "The \"Tribe Flood Network\" distributed denial of service attack tool"
[7]: https://staff.washington.edu/dittrich/misc/stacheldraht.analysis "The \"stacheldraht\" distributed denial of service attack tool"
[8]: http://home.adelphi.edu/~spock/shaft_analysis.txt "An analysis of the \"Shaft\" distributed denial of service tool"
[9]: http://securityresponse.symantec.com/avcenter/security/Content/2000_02_10_a.html "TFN2K - An Analysis (Revision : 1.3)"
---
