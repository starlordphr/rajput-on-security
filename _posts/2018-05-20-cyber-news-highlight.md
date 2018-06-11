---
layout: post
title: Cyber News Highlight - May 20, 2018
tags: [Cyber News]
---

## Arbitrary code execution vulnerability in 7-Zip
In RAR format, solid compression is very flexible such that each item of the archive can be marked as solid, independent of the other. 7-Zip uses a *solid* bit for specifying the same and the decoder object does not reinitialize its state, just utilizing the previous one.

For this to work, the decoder object needs to be initialized at the beginning and to make sure that it is, folowing code is used:

```
Byte isSolid = (Byte)((IsSolid(index) || item.IsSplitBefore()) ? 1: 0);
if (solidStart) {
  isSolid = 0;
  solidStart = false;
}

RINOK(compressSetDecoderProperties->SetDecoderProperties2(&isSolid, 1));
```

RAR handler has a method NArchive::NRar::CHandler::Extract that contains a loop iterating over a variable index and checking the boolean flag *solidStart*, with initial value *true* which makes sure that isSolid is *false* for the first item. So, the object is reinitialized whenever ```isSolid==false```, but when it is true, the object state from the previous loop iteration is used. This particular functionality can be exploited for arbitrary code execution.

RAR supports three different encoding methods and all of these methods have different objects and constructors of these objects leave a large chunk uninitialized. This is because the state needs to be reinitialized for non-solid items anyway. Consider the scenario mentioned below:
* First item encoded with method v1.
* Second item uses v2 or v3 encoding and has the *solid* bit set.

So, in this case, the first item will cause the solidStart flag to be set to false as designed. The second item with Rar2 decoder, on the other hand, is created with a large part of the decoder state uninitialized. This state can be used to load malicious binaries \[[1]\].

## Twitter's bug exposing passwords in plaintext
Twitter's backend bug exposed plaintext password of 330 million users in one of their logs. There is no report of any misuse of this information but users are advised to change their passwords immediately.

This situation occurred due to a bug in the hashing process that is used to mask the user's password before storing it in the logs. Due to the bug, plaintext passwords were stored in the internal logs \[[2]\].

## Remote hijack in Android with Rowhammer
Rowhammer attacks exploit the actual physics of how computers function. When a processor accesses a memory cell that stores electrical charge to encode a logical 1 or 0, sometimes the charge might leak to the neighboring row. This is known as bit flip and Rowhammer utilizes this phenomenon for changing some important bits.

The researchers, in this case, developed a GPU based Rowhammer attack as proof of concept. The idea was to use WebGL library in their malicious site and a timing attack over JavaScript to find out the location being accessed by the GPU. Certain locations in Firefox are treated as reference pointers to other objects and researchers used the GPU to flip the bits of these reference pointers to execute their own malicious code. This is the first time researchers were able to perform Rowhammer remotely \[[3]\].

## Restricting network access in Android P
Android, previously gave unrestricted access to the network activity of the user to all the applications installed on a smartphone. There were no fine-grained permissions designed for restricting network activity monitoring for applications. So, the applications could detect when another application connected to an external server and this unrestricted access could be used to steal network usage information.

With Android P, applications have a restricted access to proc/net and only designated VPN applications will be able to read TCP and UDP files. Unfortunately, it only affects applications targeting API 28, most of the applications will still enjoy unrestricted access until 2019, when they will be forced to change the API target level \[[4]\].

## Process injection attack in Windows CLI applications
Every time the user send Ctrl + C (or Break) signal to the console-based process, a system process *crss.exe* is invoked. The function of this process is to invoke CtrlRoutine and spawn a new thread on the targeted process.

In this process injection technique, the target malicious code is injected into a process, but it is not invoked by the adversary. Instead,  she relies on *crss.exe* to invoke it as a new thread upon Ctrl+C signal. This is important because in such a case, it will be harder for various security protections to differentiate between a valid spawned thread and the malicious one. Details on this attack have not been published yet due to the pending patch \[[5]\].

## Vulnerability in OpenPGP and S/MIME: EFAIL
EFAIL abuses active content of HTML emails, for example, externally loaded images to exfiltrate plaintext through requested URLs. To create these channels, the attacker first needs access to encrypted emails which can be obtained by eavesdropping on the network traffic. The attacker then changes the encrypted emails in a particular way and sends it to the victim. The victim's email client then decrypts the email and loads any external content, thus sending the plaintext to the attacker.

OpenPGP and S/MIME both use CBC mode of operation and the attacker can use the encrypted email body from a legitimate user and inject image tag into it. This creates a single encrypted email body that exfiltrates its own plaintext \[[6]\].

## Command injection in DHCP client of Red Hat Linux
DHCP (Dynamic Host Control Protocol) client allows a new system to obtain configurations such as an IP address and DNS server address automatically from a DHCP server. The vulnerability resides in the NetworkManager integration script included in the DHCP client packages \[[7]\].

Full details on the vulnerability have not been released yet.

---

## References
\[1\] Landave D., "7-Zip: From Uninitialized Memory to Remote Code Execution".
\[2\] The Verge, "Twitter advising all 330 million users to change passwords after bug exposed them in plain text".
\[3\] Wired, "A remote hack hijacks Android phones via electric leaks in their memory".
\[4\] Android Police, "Apps on Android P will no longer be able to monitor your network activity".
\[5\] IT Pro, "Windows-based CLI susceptible to process injection attack".
\[6\] Damian Poddebniak, Christian Dresen, Jens Müller, Fabian Ising, Sebastian Schinzel, Simon Friedberger, Juraj Somorovsky, and Jörg Schwenk., "EFAIL".
\[7\] The Hacker News, "Red Hat Linux DHCP Client Found Vulnerable to Command Injection Attacks".


[1]: https://landave.io/2018/05/7-zip-from-uninitialized-memory-to-remote-code-execution/ "7-Zip: From Uninitialized Memory to Remote Code Execution"
[2]: https://www.theverge.com/2018/5/3/17316684/twitter-password-bug-security-flaw-exposed-change-now "Twitter advising all 330 million users to change passwords"
[3]: https://www.wired.com/story/rowhammer-remote-android-attack/ "A remote hack hijacks Android phones via electric leaks in their memory"
[4]: https://www.androidpolice.com/2018/05/07/apps-android-p-will-no-longer-able-monitor-network-activity/ "Apps on Android P will no longer be able to monitor your network activity"
[5]: http://www.itpro.co.uk/security/31080/windows-based-cli-susceptible-to-process-injection-attack "Windows-based CLI susceptible to process injection attack"
[6]: https://efail.de/ "EFAIL"
[7]: https://thehackernews.com/2018/05/linux-dhcp-hacking.html "Red Hat Linux DHCP Client Found Vulnerable to Command Injection Attacks"
---
