---
layout: post
title: Mirai Botnet - The Future
tags: [Cyber Threat Watch]
---

Mirai, Japanese for "the future" is aptly named for a botnet that took control over hundreds of thousands of Internet of Things (IoT) devices starting in September 2016. At it's peak growth, Mirai infected as high as 600K IoT devices which later led to massive denial-of-service (DDoS) attacks on big targets such as Krebs on Security, OVH, and Dyn. For instance Kerbs was hit with a traffic that exceeded 600 Gbps in volume \[[1]\].

## Timeline

Mirai botnet started surfacing in early September and during mid September it started attacking OVH and Kerbs on Security. There were several other high profile attacks such as on DNS provider Dyn and Lonestar Cell, a Liberian telecom. Mirai's source code was released on hackforums.net in late September and its authors were identified in the begining of 2017. The release of Mirai's source code paved the way for other modified versions of it to exist, one of which added support for a router exploit based on CPE WAN Management Protocol, which allows for remote management of home routers. This led to an outage at Deutsche Telekom in late November 2016 with it's attacker later arrested in February 2017.

![timeline1](/assets/img/posts/CyberThreatWatch/mirai-timeline.png)

## How does Mirai work?

At its core, Mirai is a self-propogating worm, a malicious program that propogates itself by compromising devices and infecting vulnerable IoT devices. But Mirai is also a botnet because these compromised devices also can take commands from the a central command & control (C&C) servers. These servers are responsible for carrying out attacks on the target sites.

### Replication Module

Mirai spreads by sending  TCP SYN probes to pseudorandom IPv4 addresses, while excluding those that have been hardcoded into its code. This is a rapid scanning phase on Telnet TCP port 23 and 2323 and if Mirai identifies a potential victim, it enters into a brute-force login phase. During which the infected device tries to login into the potential candidate by trying 10 random pairs of stored default username and password. Originally Mirai consisted of a total list of 60 usernames and passwords. Once the target device is compromised, and the victim can be logged into, the victim's IP and credentials are sent to a harcoded report server \[[2]\].

![module11](/assets/img/posts/CyberThreatWatch/mirai-module-1.png)

After this, a loader program logs into the infected device and downloads the malware binary specific to the device's hardware and executes it for the specific hardware architecture. After a succesful injection, Mirai conceals its presence by delete the binary that is just downloaded and spoofs the name of it's process with a pseudorandom alphanumeric string. So, Mirai's infection does not persist between system reboots, moreover, it processes all the other processes that are listening on the ports 23 and 22 for TCP packets. Now the infected device, a botnet waits for any command from the C&C server.

### Attack Module

The attack module is responsible for carrying DDoS attacks on different services around the world. This module implements techniques such as HTTP flooding, UDP flooding abd all TCP flooding options.

![module21](/assets/img/posts/CyberThreatWatch/mirai-module-2.png)

## An Update On Mirai
Researchers in \[[1]\] discovered 1.2 million Mirai-infected IP addresses as of February, 2018. They found out these connections loaded 1,028 unique Mirai binaries out of which, 74% of them consisted of binaries for architectures such as MIPS 32-bit, ARM 32-bit and x86 32-bit connecting to 67 C&C servers with a total of 371 unique passwords. They found out 15,194 unique attack commands from 146 unique IP clusters.

---

## Source Code Analysis

Mirai had a hardcoded list of default usernames and passwords for devices such as variety of IoT and embedded devices ranging from DVRs, IP cameras, routers and printers. The purpose of these scans is to infiltrate vulnerable devices which have unchanged passwords. These password list is brute-force for logging into these devices with a part of the list mentioned below \[[3]\]:

```
root     xc3511
root     vizxv
root     admin
admin    admin
root     888888
root     xmhdipc
root     default
root     juantech
root     123456
root     54321
support  support
root     (none)
admin    password
root     root
root     12345
```

Mirai then launches HTTP floods and various DDoS attacks and hides its user agent information. Some of the user agent fields used are shown below:

```
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/9.1.2 Safari/601.7.7
```

Mirai also ignores some IPs that are stored in it's black list. Some of the blacklisted IPs are mentioned below:

```
127.0.0.0/8               - Loopback
0.0.0.0/8                 - Invalid address space
3.0.0.0/8                 - General Electric (GE)
15.0.0.0/7                - Hewlett-Packard (HP)
56.0.0.0/8                - US Postal Service
10.0.0.0/8                - Internal network
192.168.0.0/16            - Internal network
172.16.0.0/14             - Internal network
100.64.0.0/10             - IANA NAT reserved
169.254.0.0/16            - IANA NAT reserved
198.18.0.0/15             - IANA Special use
224.*.*.*+                - Multicast
6.0.0.0/7                 - Department of Defense
11.0.0.0/8                - Department of Defense
21.0.0.0/8                - Department of Defense
22.0.0.0/8                - Department of Defense
26.0.0.0/8                - Department of Defense
28.0.0.0/7                - Department of Defense
30.0.0.0/8                - Department of Defense
33.0.0.0/8                - Department of Defense
55.0.0.0/8                - Department of Defense
214.0.0.0/7               - Department of Defense
```

Mirai is very competing and hence consisted of many supplimentary scripts to kill other malwares which could inturn hijack these IoT devices. For example, all the processes that listen on port 22, 23 and 80 are killed. This is to avoid any other process listening on the same port and blocking Mirai out of it.

```
killer_kill_by_port(htons(23))  // Kill telnet service
killer_kill_by_port(htons(22))  // Kill SSH service
killer_kill_by_port(htons(80))  // Kill HTTP service
```

The Mirai bot also erases itself and other botnets from memory to avoid any suspicious behavior:

```
#DEFINE TABLE_MEM_QBOT            // REPORT %S:%S
#DEFINE TABLE_MEM_QBOT2           // HTTPFLOOD
#DEFINE TABLE_MEM_QBOT3           // LOLNOGTFO
#DEFINE TABLE_MEM_UPX             // \X58\X4D\X4E\X4E\X43\X50\X46\X22
#DEFINE TABLE_MEM_ZOLLARD         // ZOLLARD
```

Mirai also attacks and destroys Anime attack, which is a piece of software to compromise IoT devices. This aggresive nature helps Mirai to maximize its chance to use the botnet for it's malicious purpose also preventing any other attempt taking over.

---

## Conclusion

Mirai is an amazing piece of code, while interesting, its simple and combines both an internet worm and a botnet's behavior. One thing that everyone should learn from this is to change the default passwords on any emebedded device that you own.

---

## References
\[1\] Antonakakis M., April T., Bailey M., Bernhard M., Bursztein E., Cochran J., Durumeric Z., Halderman J. A., Invernizzi L., Kallitsis M., Kumar D., Lever C., Ma Z., Mason J., Menscher D., Seaman C., Sullivan N., Thomas K., Zhou Y., "Understanding the Mirai Botnet".
\[2\] Cloudflare, "Inside the infamous Mirai IoT Botnet: A Retrospective Analysis".
\[3\] Herzberg B., Bekerman D., Zeifman I., "Breaking Down Mirai: An IoT DDoS Botnet Analysis"

[1]: https://www.usenix.org/system/files/conference/usenixsecurity17/sec17-antonakakis.pdf "Understanding the Mirai Botnet"
[2]: https://blog.cloudflare.com/inside-mirai-the-infamous-iot-botnet-a-retrospective-analysis/ "Inside the infamous Mirai IoT Botnet: A Retrospective Analysis"
[3]: https://www.incapsula.com/blog/malware-analysis-mirai-ddos-botnet.html "Breaking Down Mirai: An IoT DDoS Botnet Analysis"
---
