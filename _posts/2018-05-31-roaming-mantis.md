---
layout: post
title: Roaming Mantis - DNS Hijacking targeting Android and iOS devices
tags: [Cyber News]
---

Discovered by security researchers at Kaspersky Lab, roaming mantis is a DNS Hijacking technique targeting poorly configured routers. Once, the router has been compromised, users are redirected by the hackers using rogue DNS settings to fake versions of legitimate websites.

A pop-up warning is then displayed with a message which says- *"To better experience the browsing, update to the latest chrome version"*. It then downloads Roaming Mantis malware on the phone as Chrome application and utilizes assigned permissions such as manage SMS, make phone calls, record audio, work with file systems, etc to collect device account information. Once installed, the application displays a fake warning which reads, *"Account No. exists risks, use after certification"*.

![android-permissions](/assets/img/posts/CyberNews/android-permissions.jpg)

Roaming Mantis then starts a local web server on the device and launches the web browser to open a fake version of google website. It then prompts the user to enter a name, date of birth, etc. To convince the user that the page is genuine, it displays the user's email id on the top of the web page \[[1]\].

![android-screen](/assets/img/posts/CyberNews/android-screen.png)

If the victim owns an iOS device, the user is redirected to a phishing website that is a fake version of legitimate Apple website and claims itself as *"security.apple.com"*. The user is then asked to enter their user id, password, card number, card expiration date, and CVV number \[[2]\].

![apple-screen](/assets/img/posts/CyberNews/apple-screen.png)

---

## References
\[1\] Khandelwal S., "Cybercriminals Hijack Router DNS to Distribute Android Banking Trojan", The Hacker News.
\[2\] Khandelwal S., "DNS-Hijacking Malware Targeting iOS, Android and Desktop Users Worldwide", The Hacker News.


[1]: https://thehackernews.com/2018/04/android-dns-hijack-malware.html "Cybercriminals Hijack Router DNS to Distribute Android Banking Trojan"
[2]: https://thehackernews.com/2018/05/routers-dns-hijacking.html "DNS-Hijacking Malware Targeting iOS, Android and Desktop Users Worldwide"
---
