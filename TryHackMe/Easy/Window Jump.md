## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
A routine vulnerability scan flagged a Windows machine on the internal network; nothing alarming on the surface, just a standard workstation left behind after a round of layoffs. IT never cleaned it up properly. Your job is to find out how badly. Your objective is to escalate from guest access all the way through:  

`guest`->`thmuser`->`notadmin`->`svcadmin`->`SYSTEM`
```
----
### IP-Address
```IP
10.49.166.146
```
----
### Enumeration
- Enumeration 
```bash
PORT      STATE SERVICE       REASON          VERSION
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
|_ssl-date: 2026-07-29T21:36:27+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=privesc
| Issuer: commonName=privesc
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-05-10T06:39:22
| Not valid after:  2026-11-09T06:39:22
| MD5:     cab5 2ba5 110d a22e 8776 fc49 279e 22b4
| SHA-1:   d83b a5cf 3b55 b9e8 4d07 0970 7465 79d6 6536 e680
| SHA-256: b5ec c096 ef74 ebbf 15b4 bd6f 020b c20c e317 07e1 89ec 3179 f3a7 33cd 96a1 d341
| -----BEGIN CERTIFICATE-----
| MIIC0jCCAbqgAwIBAgIQE6BpY6+/WbZA4drQk3++3jANBgkqhkiG9w0BAQsFADAS
| MRAwDgYDVQQDEwdwcml2ZXNjMB4XDTI2MDUxMDA2MzkyMloXDTI2MTEwOTA2Mzky
| MlowEjEQMA4GA1UEAxMHcHJpdmVzYzCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
| AQoCggEBAMbI5C2cTrbEs/BYWCA80MxQRkrqat1nLyBKKoYZjEPso2WnCzVurUMO
| XxTyuot7zw6xZDH5EJNz8pVlzRi1kaVbJ55nr13UE0POLD/pinBvirpIVTc0Z8VD
| NEgqmXYsiIGj1E8TTuTcBnRVSabGOk+6XiOBIUJNJ6jbOc6iQv6ivrWYh4ifKaSI
| 1NhEKXqSV2U+N6bbLoYSl/ubp8Okh92h0bOroqg0GZaZE2V4SQME5rU2U1Fw06az
| ARG/h/dC5FhaMVHk0RsRxTyT0fYEexD9Srb8MIDQRKGcfL+X33na2TJEX6EGACNP
| FSJTuLZSFhzO+8QIa0RsFat6A7Mm5vECAwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYB
| BQUHAwEwCwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEBCwUAA4IBAQBRtsZ3Igj8nm0V
| lFNp0DYT8mtvq7GC7/C68psBRYQckgoPvjt3R7DG8nAigAVEvFCME8u6pM6aR8Yo
| CQWN+MpR9k5nr+JkblN3YOQi8vAijNPnPpn5vRrblWzAY5SzDwYx3AzKUX70aHog
| T6As9YlJ6cOLZaaV9AXAFeAAzzYY9OmNF00ch9zi5ZcOjA6kR6u9QZ0y10CntgdB
| uZsjClGNRAOGGSQ1wxCq6vHUn7f6k8zPSvJDGkUbiloUreXtLJdhOoVDYmlFn9Ps
| kmRJift5BjU5ptKpG4EtRBbaVeb2ctlmlZoO3plXLQQbFxtVGN4mbEtQpEBxmhto
| lntb9Cm4
|_-----END CERTIFICATE-----
| rdp-ntlm-info:
|   Target_Name: PRIVESC
|   NetBIOS_Domain_Name: PRIVESC
|   NetBIOS_Computer_Name: PRIVESC
|   DNS_Domain_Name: privesc
|   DNS_Computer_Name: privesc
|   Product_Version: 10.0.17763
|_  System_Time: 2026-07-29T21:36:19+00:00
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp  open  pando-pub?    syn-ack ttl 126
47001/tcp open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC

Host script results:
| smb2-time:
|   date: 2026-07-29T21:36:23
|_  start_date: N/A
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required
|_clock-skew: mean: 0s, deviation: 0s, median: 0s
| p2p-conficker:
|   Checking for Conficker.C or higher...
|   Check 1 (port 9206/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 58666/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 52116/udp): CLEAN (Timeout)
|   Check 4 (port 14109/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
```
- Host Name
```bash
Host:
- Computer Name: `PRIVESC`
- Domain: `PRIVESC` (likely standalone/workgroup)
- OS: Windows Server 2019 / Windows 10 build 17763
```
----
### Enumeration SMB
- Checking available shares and can we access it.
```bash
nxc smb 10.49.166.146
SMB         10.49.166.146   445    PRIVESC          [*] Windows 10 / Server 2019 Build 17763 x64 (name:PRIVESC) (domain:privesc) (signing:False) (SMBv1:None)
```
 - Shares
```bash
$ smbclient -L //10.49.166.146/ -N

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	Public          Disk      Public file share
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.49.166.146 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
- Public Share
```bash
 smbclient //10.49.166.146/Public -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon May 11 02:40:51
  ..                                  D        0  Mon May 11 02:40:51
  welcome.txt                         A      177  Mon May 11 02:40:50

smb: \> get welcome.txt
$ cat welcome.txt
Welcome to CORP-NET.

New employee default credentials
================================
Username : thmuser
Password : [password omitted]
Please change your password after first login.
```
- Enumerate 
```bash
$ nxc smb 10.49.166.146 -u thmuser -p '[password omitted]'
SMB         10.49.166.146   445    PRIVESC          [*] Windows 10 / Server 2019 Build 17763 x64 (name:PRIVESC) (domain:privesc) (signing:False) (SMBv1:None)
SMB         10.49.166.146   445    PRIVESC          [+] privesc\thmuser:[password omitted]
```
- xfreedp
```bash
$ xfreerdp /v:10.49.166.146 /u:thmuser /p:'[password omitted]' /cert:ignore
PS C:\Users\thmuser.PRIVESC> whoami
privesc\thmuser
PS C:\Users\thmuser.PRIVESC> cd ..
PS C:\Users> ls
Directory: C:\Users
Mode                         Length Name
d-----                       Administrator
d-----                     notadmin
d-----                       notadmin.PRIVESC
d-r---                       Public
d-----                     svcadmin
d-----                      svcadmin.PRIVESC
d-----                      thmuser
d-----                     thmuser.PRIVESC

PS C:\Users> cd .\thmuser\
PS C:\Users\thmuser> ls
Directory: C:\Users\thmuser
Mode                         Length Name                  
d-----                        Desktop               
PS C:\Users\thmuser> cd .\Desktop\ 
PS C:\Users\thmuser\Desktop> ls                                        
Directory: C:\Users\thmuser\Desktop                                    
Mode                         Length Name
-a----        5/11/2026   6:41 AM             27 flag1.txt             
PS C:\Users\thmuser\Desktop> type flag1.txt
[flag omitted] 
```
----
### User - `thmuser`
- Enumerate for Escalation
- User Privileges (`whoami /priv`)

| Privilege Name                    | Description                    | State    |
| :-------------------------------- | :----------------------------- | :------- |
| **SeChangeNotifyPrivilege**       | Bypass traverse checking       | Enabled  |
| **SeIncreaseWorkingSetPrivilege** | Increase a process working set | Disabled |

---
### Group Information (`whoami /groups`)

| Group Name | Type | SID | Attributes |
| :--- | :--- | :--- | :--- |
| **Everyone** | Well-known group | `S-1-1-0` | Mandatory group, Enabled by default, Enabled group |
| **BUILTIN\Remote Desktop Users** | Alias | `S-1-5-32-555` | Mandatory group, Enabled by default, Enabled group |
| **BUILTIN\Users** | Alias | `S-1-5-32-545` | Mandatory group, Enabled by default, Enabled group |
| **NT AUTHORITY\REMOTE INTERACTIVE LOGON** | Well-known group | `S-1-5-14` | Mandatory group, Enabled by default, Enabled group |
| **NT AUTHORITY\INTERACTIVE** | Well-known group | `S-1-5-4` | Mandatory group, Enabled by default, Enabled group |
| **NT AUTHORITY\Authenticated Users** | Well-known group | `S-1-5-11` | Mandatory group, Enabled by default, Enabled group |
| **NT AUTHORITY\This Organization** | Well-known group | `S-1-5-15` | Mandatory group, Enabled by default, Enabled group |
| **NT AUTHORITY\Local account** | Well-known group | `S-1-5-113` | Mandatory group, Enabled by default, Enabled group |
| **LOCAL** | Well-known group | `S-1-2-0` | Mandatory group, Enabled by default, Enabled group |
| **NT AUTHORITY\NTLM Authentication** | Well-known group | `S-1-5-64-10` | Mandatory group, Enabled by default, Enabled group |
| **Mandatory Label\Medium Mandatory Level** | Label | `S-1-16-8192` | — |
