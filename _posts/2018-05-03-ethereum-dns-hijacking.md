---

layout: post
title: Ethereum Wallet Client Attacked by DNS Hijacking
tags: [Cyber News]

---

Recent events following reports that Ethereum wallet client was attacked using DNS Hijacking has made one thing clear that security is as strong as the people who understand it’s importance. Before we go into the details of this attack, let’s talk about some background.

## DNS
DNS stands for *Domain Name Server* and these servers are an important backbone of internet as we know it. Whenever we enter a website url in our browser, a query request is sent for mapping the IP address corresponding to the url entered. Since servers on the internet are identified by IP addresses, resolving DNS is generally the first step of loading a website.

For scalability, DNS was designed as a tree structure. At the top of the tree there is the root server "." and there are 13 such root servers worldwide. On a level lower, *Top Level Domain (TLD) Servers* consists of subdomains such as edu, com, info, org, etc. The lowest level of *Name Server* consists of specific domains. For instance consider *www.rajputonsecurity.com*, where "." is resolved root server,  *com* is resolved by TLD server and *rajputonsecurity* is resolved by the name servers.

![dns-tree](/assets/img/posts/CyberNews/dns-tree.png)

## Resolving DNS
Let’s consider the url *www.rajputonsecurity.com* in our discussion. For resolving a DNS query, the web browser first contacts the recursive name server which searches its cache for an entry. If no matching entry is found, this server sends a request to the root server for a reply. The root server then replies with the IP address of the DNS server holding the record for *com*. Recursive DNS server then contacts the TLD name server for an address to the server holding the record for *rajputonsecurity*. Then finally the recursive server queries this name server to get the exact IP address corresponding to *rajputonsecurity.com*.

![dns-resolve-1](/assets/img/posts/CyberNews/dns-resolve.png)

---

## DNS Hijacking
DNS hijacking is an attack where adversaries replace original IP address corresponding to a url with their IP address redirecting legitimate users to malicious website \[[1]\]. This usually occurs because DNS entries are not commonly controlled by the websites themselves but instead by third parties and sometimes they do not verify the identity for a request of change of IP address. DNS hijacking can be used by the adversary to phish usernames, passwords and sometimes to also install malware on the victim’s system.

### Case Study: Ethereum Wallet Client
Adversaries used DNS hijacking attack to redirect users to a malicious client where the users entered their private keys. These private keys were then stolen by the attackers which resulted in a total loss of $150,000.  It is still unclear how the attackers changed the DNS entry to point to their malicious clients \[[2]\].

### Case Study: panix.com Incident
Internet Service Provider, *Public Access Networks Corporation* fell victim to DNS hijacking back in 2005, where the management of the domain was transferred from Dotster Inc. to Melbourne IT, by an unauthenticated request from a Melbourne IT reseller without the knowledge and the consent of the registrant.

### Case Study: hushmail.com Incident
In 2005, adversaries convinced tier 1 staff of Network Solutions, Inc. to modify the administrative email contact connected to the Hush registration record. Using this new email, the attackers submitted a password reset request, changed the password, and then the DNS record.

### Case Study: Nike.com Incident
Adversaries in this case spoofed email and used that to transfer the domain to a malicious one. This led to denial of service to third parties.

There are many more such cases where DNS hijacking has led to phishing of usernames and passwords, stealing secret key, providing false information or just denial of service. Until we use strict authentication mechanisms for changing DNS records, there are more such attacks yet to come.

---

## References
\[1\] Andy Greenberg Security, "Hacker Lexicon: What is DNS Hijacking?".<br />
\[2\] Wilmoth J., "Ethereum Wallet Client MyEtherWallet Succumbs to DNS Hijacking Attack".<br />

[1]: https://www.wired.com/story/what-is-dns-hijacking/ "Hacker Lexicon: What is DNS Hijacking?"
[2]: https://www.ccn.com/ethereum-wallet-client-myetherwallet-succumbs-to-dns-hijacking-attack/ "Ethereum Wallet Client MyEtherWallet Succumbs to DNS Hijacking Attack"

---
