---
layout: post
title: VPNFilter - A Sophisticated Modular Malware
tags: [Cyber News]
---

For several months, Talos (Cisco's cyber threat intelligence team) has been working with its partners to monitor a sophisticated modular malware called "VPNFilter". The code of this malware overlaps with different versions of BlackEnergy malware - which was responsible for large-scale attacks that targeted devices in Ukraine.

![vpnfilter-1-1](/assets/img/posts/CyberNews/vpnfilter-1-1.jpg)

In stage 1, VPNFilter malware targets devices running firmware based on Linux and Busybox and it is compiled for several CPU architectures. The main purpose of the first stage is to connect to a server providing a more fully featured second stage. The malware is capable of modifying non-volatile configuration memory (NVRAM) values and adds itself to crontab, the Linux job scheduler for achieving persistence. The Command-and-Control (c2) communication is encrypted or over the Tor network. The binary itself is not encrypted but some of the strings used are encrypted and are only decrypted during runtime. To get the IP address of the server, malware downloads the first image from a gallery specified by a hard-coded URL of *Photobucket.com* and extracts the download server IP address from six integer values for GPS latitude and longitude in the EXIF information. If the connection fails, the malware reaches out to a backup domain, *toknowall\[.\]com*. If the second attempt fails, the malware opens a listener and checks its public IP from *api.ipify.org* and stores it for later comparison. The listener then checks all the packets that arrive on any port to identify the trigger. If the received packet meets a predefined set of criteria, it will extract an IP address from the packet and attempt a stage 2 download.

In stage 2, the malware first sets up the working environment by creating a directory for modules (/var/run/vpnfilterm) and a working directory (/var/run/vpnfilterw). Then, it runs in a loop and reaches out to C2 server for retrieving commands. The commands are encrypted with a broken RC4 function as in stage 1. Some of the commands are shown below:
* kill: Overwrites the first 5,000 bytes of /dev/mtdblock0 with zeros, and reboots the device (effectively bricking it).
* exec: Executes a shell command or plugin.
* tor: Sets the Tor configuration flag (0 or 1).
* copy: Copies a file from the client to the server.
* seturl: Sets the URL of the current configuration panel.
* proxy: Sets the current proxy URL.
* port: Sets the current proxy port.
* delay: Sets the delay between main loop executions.
* reboot: Reboots the device if it has been up for more than 256 seconds, and the build name is specified in the parameter.
* download: Downloads a URL to a file. This can be applied to all devices or just a certain build name.

Stage 3 consists of two plugin modules used for sniffing packets and communicating over the tor network. Researchers at Talos believe that there might be several other plugins that are yet to be discovered. Only the stage 1 is a persistent stage and all the remaining stages can be undone by a simple restart of the router or your NAS storage device. Consumers have been advised to change default credentials and to reset their devices and update the firmware if not done so \[[1]\].

On 22 May 2018, FBI agents in Pittsburg asked federal Magistrate Judge Lisa Pupo Lenihan for an order directing the domain registration firm Verisign to hand the *toknowall\[.\]com* address over to FBI to disrupt the ongoing criminal investigation involving the use of botnet and to assist remediation efforts \[[2]\].

---

## References
\[1\] Largent W., "New VPNFilter malware targets at least 500K networking devices worldwide", Talos Intelligence.
\[2\] Poulsen K., "Exclusive: FBI Seizes Control of Russian Botnet", Daily Beast.

[1]: https://blog.talosintelligence.com/2018/05/VPNFilter.html "New VPNFilter malware targets at least 500K networking devices worldwide"
[2]: https://www.thedailybeast.com/exclusive-fbi-seizes-control-of-russian-botnet "Exclusive: FBI Seizes Control of Russian Botnet"
---
