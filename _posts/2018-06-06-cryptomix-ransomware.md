---

layout: post
title: A New Variant of Backup Cryptomix Ransomware
tags: [Cyber News]

---

The original version of Backup Cryptomix Ransomware mostly spread using email phishing attack. These e-mail campaigns use deceptive tactics to trick users into downloading and opening the attachment. As soon as the attachment is open, the payload is initiated and it starts infecting the machine.

The malware drops a payload in the %ProgramData% directory which stops certain critical Windows services that are responsible for backups. The following stop commands are run:
```
sc stop VVS
sc stop wscsvc
sc stop WinDefend
sc stop wuauserv
sc stop BITS
sc stop ERSvc
sc stop WerSvc
cmd.exe /C bcdedit /set {default} recoveryenabled No
cmd.exe /C bcdedit /set {default} bootstatuspolicy ignoreallfailures
C:\Windows\System32\cmd.exe” /C vssadmin.exe Delete Shadows /All /Quiet
```

The last command aims to delete the shadow volume copies to make the recovery process even harder.

The .BACKUP malware starts the encryption process by first scannning the files it aims to encrypt. The files include videos, documents, archives and much more. A complete list of extenions are mentioned below:
```
“PNG .PSD .PSPIMAGE .TGA .THM .TIF .TIFF .YUV .AI .EPS .PS .SVG .INDD .PCT .PDF .XLR .XLS .XLSX .ACCDB .DB .DBF .MDB .PDB .SQL .APK .APP .BAT .CGI .COM .EXE .GADGET .JAR .PIF .WSF .DEM .GAM .NES .ROM .SAV CAD Files .DWG .DXF GIS Files .GPX .KML .KMZ .ASP .ASPX .CER .CFM .CSR .CSS .HTM .HTML .JS .JSP .PHP .RSS .XHTML. DOC .DOCX .LOG .MSG .ODT .PAGES .RTF .TEX .TXT .WPD .WPS .CSV .DAT .GED .KEY .KEYCHAIN .PPS .PPT .PPTX ..INI .PRF Encoded Files .HQX .MIM .UUE .7Z .CBR .DEB .GZ .PKG .RAR .RPM .SITX .TAR.GZ .ZIP .ZIPX .BIN .CUE .DMG .ISO .MDF .TOAST .VCD SDF .TAR .TAX2014 .TAX2015 .VCF .XML Audio Files .AIF .IFF .M3U .M4A .MID .MP3 .MPA .WAV .WMA Video Files .3G2 .3GP .ASF .AVI .FLV .M4V .MOV .MP4 .MPG .RM .SRT .SWF .VOB .WMV 3D .3DM .3DS .MAX .OBJ R.BMP .DDS .GIF .JPG ..CRX .PLUGIN .FNT .FON .OTF .TTF .CAB .CPL .CUR .DESKTHEMEPACK .DLL .DMP .DRV .ICNS .ICO .LNK .SYS .CFG”
```

All the encrypted files are saved with a `.AZER` extension. The malware then saves an instruction file `_HELP_INSTRUCTION.txt` and asks the victim to contact them via email for decryption \[[1]\]. The content of the instruction file is mentioned below:
```
Attention! All Your data was encrypted!

For specific informartion, please send us an email with Your ID number:

file1@keemail.me
file1@protonmail.com
file1m@yandex.com
file1n@yandex.com
file1@techie.com
Please send email to all email addresses! We will help You as soon as possible!

IMPORTANT: DO NOT USE ANY PUBLIC SOFTWARE! IT MAY DAMAGE YOUR DATA FOREVER!

DECRYPT-ID-{RANDOM NUMBER}
```

In the new variant of this malware, the encryption method essentially remains the same but the email ids in the ransom note have been changed to the following:
```
backuppc@tuta.io
backuppc@protonmail.com
backuppc1@protonmail.com
b4ckuppc1@yandex.com
b4ckuppc2@yandex.com
backuppc1@dr.com
```

Another noticeable difference is the file extension that is added to the encrypted files. In the new variant after the file is encrypted, the `.BACKUP` extension is added to the file \[[2]\].


---

## References
\[1\] Paganini P., Security Affairs, "Experts from Bleeping Computer spotted a new Cryptomix Ransomware variant".<br />
\[2\] Abrams L., Bleeping Computer, "New Backup Cryptomix Ransomware Variant Actively Infecting Users".<br />

[1]: https://securityaffairs.co/wordpress/67103/malware/file-cryptomix-ransomware.html "Experts from Bleeping Computer spotted a new Cryptomix Ransomware variant"
[2]: https://www.bleepingcomputer.com/news/security/new-backup-cryptomix-ransomware-variant-actively-infecting-users/ "New Backup Cryptomix Ransomware Variant Actively Infecting Users"

---
