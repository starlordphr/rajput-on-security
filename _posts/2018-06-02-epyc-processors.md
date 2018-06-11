---
layout: post
title: Attack on Epyc Processors and EOS
tags: [Cyber News]
---

## Attack on AMD's Virtual Machine Encryption

German researchers have devised a method to bypass the security mechanism used by AMD's Epyc server chips. These chips support a feature known as Secure Encrypted Virtualization. This feature is responsible for encrypting and decrypting virtual machines (VM) on the fly when they are stored in RAM. Due to this, the host operating system, hypervisor and any other malware on the host machine cannot steal any information on the encrypted VM. Each of these has an assigned address space ID which is linked to a cryptographic key that is used to encrypt the data as it moves out of the VM and to decrypt the data as it moves in. The key never leaves the system-on-chip and each VM gets a separate key, ensuring security \[[1]\].

Researchers recently were demonstrated that its possible to extract memory contents from the encrypted VMs in plaintext. They explain in \[[8]\] that while the address translation between the Guest Virtual Address to Guest Physical Address is controlled by the VM itself, Hypervisor controls the Second Level Address Translation. This maintains the VM's Guest Physical Address to Host Physical Address Mapping. Page-wise encryption of main memory in these processors lacks integrity protection and hence it enables an adversary to change the memory layout of the VM in HV and trick the VM to return its arbitrary pages in plain text just by using a web server.

This seems like a very interesting idea and we will talk about this in a separate post where we will understand \[[2]\] in depth.

---

## EOS SuperNode Attack
360 Vulcan team discovered a series of high-risk security vulnerabilities in the EOS blockchain platform. One of such reported attack was a supernode attack in which the adversary could construct and publish a smart contract containing malicious code. The EOS supernode would execute this malicious code and trigger a security hole. The attacker can then re-use the supernode to package the malicious contract into multiple new blocks and the whole network of nodes can be compromised by the adversary. The attacker in such a scenario has complete control over the system of nodes and can steal secret keys for the EOS supernode, control virtual currency transaction, steal user's key stored in the wallet and much more. Moreover, the attacker can turn a node in EOS network into a member of a botnet and launch attacks \[[3]\].

---

## References
\[1\] Nicholas S., "Epyc fail? We can defeat AMD's virtual machine encryption, say boffins", The A Register.
\[2\] Morbitzer M., Huber M., Horsch J., Wessel S., "SEVered: Subverting AMD’s Virtual Machine Encryption".
\[3\] 360 Vulcan Team,360 finds blockchain epic vulnerabilities to fully control virtual currency transactions

[1]: https://www.theregister.co.uk/2018/05/25/amd_epyc_sev_vm_encryption_bypass/ "Epyc fail? We can defeat AMD's virtual machine encryption, say boffins"
[2]: https://arxiv.org/pdf/1805.09604.pdf "SEVered: Subverting AMD’s Virtual Machine Encryption"
[3]: http://www.360.cn/newslist/zxzx/360fxqklssjldkwqkzxnhbjy.html "360 finds blockchain epic vulnerabilities to fully control virtual currency transactions"
---
