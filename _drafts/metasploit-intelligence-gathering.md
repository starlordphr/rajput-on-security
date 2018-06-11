---
layout: post
title: Metasploit - Intelligence Gathering
tags: [Capture The Flag]
---

Metasploit is an open source framework for performing penetration testing. It provides many utilities for intelligence gathering, payload design and methods to deliver the payload.

Some important terminology:

1. *Exploit*: An exploit is the means by which an attacker can take advantage of a certain flaw within a system. The attacker uses an exploit to attack a system such that it results in a particular desired outcome.
2. *Payload*: A payload is a code that we want to execute on the victim’s system and which has to be delivered by the framework.
3. *Shellcode*: Shellcode is the set of instructions used as a payload when exploitation occurs. This is generally written in assembly language.
4. *Listener*: A listener is a component within Metasploit that waits for an incoming connection from any compromised victim’s system.

Before going into the details of metasploit, lets first talk a little bit about the phases of *Penetration Testing Execution Standard (PTES)*:

### Pre-engagement Interactions
*Pre-engagement interactions* typically occur when the parties involved are discussing the scope of the penetration testing. This is important because the client has to be informed if the plan is going to involve overt or covert penetration testing.

### Intelligence Gathering
This phase involves gathering information about the organization taking part in the penetration testing. This is done by using social media networks, gathering information about the servers, footprinting the target, etc. The main aim of this phase is to try and identify the protection mechanisms in place , identify the system and gather as much information that can be obtained. This is one of the most important phase of PTES as this enables the red team to design effective payloads based on the information gathered.

### Threat Modeling
*Threat Modeling* uses information collected from intelligence gathering to identify any vulnerability that can be exploited. These vulnerabilities genrally arise due to legacy software and hardware, misconfiguration of firewalls, services, etc.

### Vulnerability Anlaysis
After identification of viable attack vectors, during vulnerability analysis penetration testers analyze what attacks could be the most viable based on the information gathered in the previous phases. This takes into account information gathered from port and vulnerability scans.

### Exploitation
During this phase an exploit is delivered by using a payload and if successful, a connection is made between the victim and the adversary. There is always a chance of unforeseen protective measures and the payload might get flagged by an Intrustion Detection System (IDS). The attacker can then again modify the payload to pass the IDS and make an impact.

### Post Exploitation
After the victim is compromised, the penetration testers have to gather valuable information from the testing. Testers should take time to analyze the information that has been gathered such as what do some systems do and the different users that login into the system and their roles.

### Reporting
During this phase, testers communicate the process followed during the penetration testing and the vital information that was gathered. Generally the report is divided into executive summary, executive presentation and technical findings. The technical findings should be stated such that they are easy to follow and technical enough to help the IT team to fix the security holes in the company’s infrastructure.

## Types of Penetration Tests
There are two main types of penetration tests:

1. *Overt Penetration Testing*: During this type of testing the penetration testers work with the organization to identify potential security threats and the IT infrastructure of the organization is already known to the testers. One of the benifits of overt testing is that the IT team of the organization can work with the testers to fix any security loop holes, moreover since the attacks will be process aware due to the knowledge of the infrastructure, they can be sophisticated. A potential downside is that the response effectiveness of the IT team cannot be measured in such scenarios.
2. *Covert Penetration Testing*: This type of testing is done to simulate the steps followed by an actual attacker. The testers try to attack without the knowledge of most of the organization. This is a good way to measure the response strategies followed by an organization's IT department incase of a breach.

## MSF Console
*Msfconsole* is the most popular part of Metasploit as it is flexible, feature rich and very well documented. It provides an all-in-one interface to almost every option and setting available in the framework.

Most of our focus will be on Msfconsole and the code snippet in this tutorial are all done in it.

## Passive Information Gathering
Passive information gathering refers to the process in which information about target is obtained without touching it. *Opensource intelligence (OSINT)* is a form of intelligence collection that uses already available open source tools to acquire information about the target.

### whois Lookups
*whois* is a query and response protocol that is used to query databases that store information about registered users of an internet resources such as domain name, an IP address book, etc.

```
msf > whois rajputonsecurity.com
[*] exec: whois rajputonsecurity.com

Domain Name: RAJPUTONSECURITY.COM
Registry Domain ID: 2221250057_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.google.com
Registrar URL: http://domains.google.com
Updated Date: 2018-02-05T06:19:58Z
Creation Date: 2018-01-31T06:10:45Z
Registry Expiry Date: 2019-01-31T06:10:45Z
Registrar: Google Inc.
Registrar IANA ID: 895
Registrar Abuse Contact Email: registrar-abuse@google.com
Registrar Abuse Contact Phone: +1.8772376466
Domain Status: ok https://icann.org/epp#ok
Name Server: LIZ.NS.CLOUDFLARE.COM
Name Server: TODD.NS.CLOUDFLARE.COM
DNSSEC: unsigned
URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2018-04-27T21:04:31Z <<<
```

### NSLookup
*NSLookup* is used to get additional information about the servers.

```
msf > nslookup
[*] exec: nslookup

> set type=mx
> rajputonsecurity.com
> Server:	209.222.18.222
Address:	209.222.18.222#53

Non-authoritative answer:
rajputonsecurity.com	mail exchanger = 1 aspmx.l.google.com.
rajputonsecurity.com	mail exchanger = 5 alt1.aspmx.l.google.com.
rajputonsecurity.com	mail exchanger = 5 alt2.aspmx.l.google.com.
rajputonsecurity.com	mail exchanger = 10 alt3.aspmx.l.google.com.
rajputonsecurity.com	mail exchanger = 10 alt4.aspmx.l.google.com.
```

## Active Information Gathering
In active information gathering, the tester interacts directly with the system that has to be compromised, This might include port scans to detect the services running or to detect if an SQL server is running on the target system.

For demonstrating active scanning, we will move to my testbed which consists of a Kali Linux instance that is the penetration tester’s main system, two Ubuntu 16.04 based VM with various ports open but no services listening to those ports yet and a Windows VM that has a SQLExpress Server configured.

### Port Scanning with Nmap
Here we begin by scanning for open ports on the remote target. *Port Scanning* is a process where we connect to each and every port on the remote to see if those are open. These ports can give the tester an idea about the services that are running on the remote. This helps the attacker to design payloads specific to the vulnerable service and exploit the vulnerability to gain access.

*Nmap* is one of the most popular port scanning tool and is integrated within Metasploit quite elegantly.

```
[*] exec: nmap -sS -Pn 10.13.13.101


Starting Nmap 7.60 ( https://nmap.org ) at 2018-04-27 17:24 EDT
Nmap scan report for 10.13.13.101
Host is up (0.00026s latency).
Not shown: 990 filtered ports
PORT STATE SERVICE
21/tcp closed ftp
25/tcp closed smtp
80/tcp closed http
135/tcp closed msrpc
139/tcp closed netbios-ssn
443/tcp closed https
445/tcp closed microsoft-ds
1025/tcp closed NFS-or-IIS
1433/tcp closed ms-sql-s
3389/tcp closed ms-wbt-server
MAC Address: 08:00:27:5A:B0:13 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 21.85 seconds
```

Here -sS specifies namp to run a stealth scan, -Pn specifiec not to use ping to determine weather a system is running. Ping uses Internet Control Message Protocol (ICMP) and most of the networks don't allow ICMP. This is where the specified option should be used whenever performing internet-based penetration testing.

For getting more information -A flag can be used with *nmap*. This option will attempt advanced service enumeration and banner grabbing, which might give additional information about the victim's system.

```
msf > nmap -sS -Pn -A 10.13.13.101
[*] exec: nmap -sS -Pn -A 10.13.13.101


Starting Nmap 7.60 ( https://nmap.org ) at 2018-04-27 17:28 EDT
Nmap scan report for 10.13.13.101
Host is up (0.00027s latency).
Not shown: 990 filtered ports
PORT STATE SERVICE VERSION
21/tcp closed ftp
25/tcp closed smtp
80/tcp closed http
135/tcp closed msrpc
139/tcp closed netbios-ssn
443/tcp closed https
445/tcp closed microsoft-ds
1025/tcp closed NFS-or-IIS
1433/tcp closed ms-sql-s
3389/tcp closed ms-wbt-server
MAC Address: 08:00:27:5A:B0:13 (Oracle VirtualBox virtual NIC)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

TRACEROUTE
HOP RTT ADDRESS
1 0.27 ms 10.13.13.101

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.72 seconds
```

### TCP Idle Scan with Nmap
A more stealthy approach for scanning ports with *namp* is to use TCP idle scan. The main idea of the approach is to use a zombie host on the network by spoofing its IP address. So, the attacker first finds out the initial IP ID (used to track the packet order) of the zombie host and then spoofs the IP address and IP ID of the zombie host and sends a packet to the victim. If the specific port being scanned is open, the victim replies to the zombie host and we can measure the increment in the IP ID to infer if the port on the victim machine is open.

An increase of one in the IP ID represents that the zombie host has sent the packet but an increase of two in IP ID represents a response from the victim as well. This means that the corresponding port on the victim’s system is open.

If the zombie host is accessible, *namp* proceeds regularly, but if it is not accessible, following message is displayed:

```
msf auxiliary(scanner/ip/ipidseq) > nmap -PN -sI 10.13.13.101 10.13.13.105
[*] exec: nmap -PN -sI 10.13.13.101 10.13.13.105


Starting Nmap 7.60 ( https://nmap.org ) at 2018-04-27 18:32 EDT
Idle scan zombie 10.13.13.101 (10.13.13.101) port 80 cannot be used because it has not returned any of our probes -- perhaps it is down or firewalled.
QUITTING!
```

## Targeted Scanning
These types of scans look for a specific operating system, service, program version or configuration based on the vulnerability found in them. Often systems are not updated regularly and even a well known bug can be exploited to gain access into the system.

### Server Message Block Scanning
Metasploit can be used to fingerprint the version of Microsoft Windows using its smb_version module.

```
msf auxiliary(scanner/ftp/anonymous) > use scanner/smb/smb_version
msf auxiliary(scanner/smb/smb_version) > set RHOSTS 10.13.13.106
RHOSTS => 10.13.13.106
msf auxiliary(scanner/smb/smb_version) > run

[+] 10.13.13.106:445 - Host is running Windows 10 Pro (build:16299) (name:DESKTOP-QGHI32N) (workgroup:WORKGROUP )
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Using this build version information, an attacker can exploit known flaws in systems that are not updated.

### Poorly Configured Microsoft SQL Servers
When MS SWL is installed, it listens on TCP port 1433 or on a random dynamic TCP port. If MS SQL is listening on a dynamic port, simply query UDP port 1434 to discover the dynamic TCP port being used by it.

```
msf auxiliary(scanner/smb/smb_version) > use scanner/mssql/mssql_ping
msf auxiliary(scanner/mssql/mssql_ping) > set RHOSTS 10.13.13.106
RHOSTS => 10.13.13.106
msf auxiliary(scanner/mssql/mssql_ping) > run

[*] 10.13.13.106: - SQL Server information for 10.13.13.106:
[+] 10.13.13.106: - ServerName = DESKTOP-QGHI32N
[+] 10.13.13.106: - InstanceName = SQLEXPRESS
[+] 10.13.13.106: - IsClustered = No
[+] 10.13.13.106: - Version = 14.0.1000.169
[+] 10.13.13.106: - tcp = 1433
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

There are many other modules for SSH scanning, FTP scanning and Simple Network Management Protocol Sweeping for intelligence gathering.

---

## Conclusion
In this post we discussed in brief about the process of penetration testing and how to use Metasploit for gathering valuable intel for penetration testing. In the next post of this series we will discuss about Vulnerability Scanning.
---
