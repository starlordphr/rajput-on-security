---

layout: post
title: Arbitrary Code Execution using Vulnerability in Cortana
tags: [Cyber News]

---

Windows recently patched a vulnerability (June 12) that otherwise allowed adversaries to execute arbitrary code using Cortana, which could be further exploited to even log into to user's account.

Cortana is the helpful personal assistant built into Windows 10 and a recent update allowed it to be triggered even from the lock screen. Also, Windows indexes files in the background with various file extensions. These files and some of its content is then available in Cortana's search result. This can then be used to retrieve sensitive information without logging into the user's account by searching for keywords such as "PAS".

![Stealing Secret Information](/assets/img/posts/CyberNews/cortana-code-1.png)

Cortana changes search results according to the query specified by the user and hence it is a context-aware search. Following steps can be used to execute arbitrary code on victim's machine:
* Land a PowerShell script in a location that will be indexed. This can be a shared drive, or one drive of the user or even a USB. These locations are also generally indexed by Windows.
* Execute a search query that will show the document and click on it by saying the command “Hey Cortana, PS1”. Then select the PowerShell script you just indexed and left click. The PowerShell script then opens in Notepad.

![Arbitrary Code Execution](/assets/img/posts/CyberNews/cortana-code-4.png)

* Execute a search query that will show the recent documents, right click and using Cortana, type or search in the contextual menu for “txt”. Right click on the PowerShell script in the Recent category under the Apps tab at the top (not Documents) and click on “Run with PowerShell”.

![Arbitrary Code Execution](/assets/img/posts/CyberNews/cortana-code-3.png)

Using similar steps, a Powershell script can be written with **HIGH** integrity to reset victim's login credentials just by using Cortana.**HIGH** integrity allows a script to be executed without User Account Control (UAC) \[[1]\].

## References
\[1\] Cochin C., Povolny S., "Want to Break Into a Locked Windows 10 Device? Ask Cortana (CVE-2018-8140)".

[1]: https://securingtomorrow.mcafee.com/mcafee-labs/want-to-break-into-a-locked-windows-10-device-ask-cortana-cve-2018-8140/ "Want to Break Into a Locked Windows 10 Device? Ask Cortana (CVE-2018-8140)"
