## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Season - 11 Machine 
Reactor  - Medium   - Window
```
## IP-Address
```Machine Information
As is common in real life pentests, you will start the Checkpoint box with credentials for the following account alex.turner / [password omitted]

10.129.108.38
```
---
### Enumeration
- Port Scanning
```bash
PORT      STATE SERVICE           REASON          VERSION
53/tcp    open  domain            syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec      syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2026-06-20 09:09:23Z)
135/tcp   open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn       syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: checkpoint.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?     syn-ack ttl 127
464/tcp   open  kpasswd5?         syn-ack ttl 127
593/tcp   open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?          syn-ack ttl 127
3268/tcp  open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: checkpoint.htb, Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl? syn-ack ttl 127
5985/tcp  open  http              syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf            syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49670/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49675/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49676/tcp open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49679/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49706/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49715/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
```
--- 
#### SMB Enumeration
- List all the directory
```bash
─$ smbmap -H $TARGET -u alex.turner -p '[password omitted]'
[+] IP: 10.129.108.38:445	Name: 10.129.108.38       	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C$                                                	NO ACCESS	Default share
	DevDrop                                           	READ ONLY	VS Code extensions share for approved .vsix packages compatible with VS Code engine 1.118.0
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share
	SYSVOL                                            	READ ONLY	Logon server share
	VMBackups                                         	NO ACCESS	

$ netexec ldap $TARGET -u alex.turner -p '[password omitted]'
LDAP        10.129.108.38   389    DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:checkpoint.htb) (signing:Enforced) (channel binding:No TLS cert)
LDAP        10.129.108.38   389    DC01             [+] checkpoint.htb\alex.turner:[password omitted]
```
---
#### User Enumeration
```bash
$ netexec ldap $TARGET -u alex.turner -p '[password omitted]' --users
LDAP        10.129.108.38   389    DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:checkpoint.htb) (signing:Enforced) (channel binding:No TLS cert)
LDAP        10.129.108.38   389    DC01             [+] checkpoint.htb\alex.turner:[password omitted]
LDAP        10.129.108.38   389    DC01             [*] Enumerated 17 domain users: checkpoint.htb
LDAP        10.129.108.38   389    DC01             -Username-                    -Last PW Set-       -BadPW-  -Description-
LDAP        10.129.108.38   389    DC01             Administrator                 2026-05-09 12:16:34 0        Built-in account for administering the computer/domain
LDAP        10.129.108.38   389    DC01             Guest                         <never>             0        Built-in account for guest access to the computer/domain
LDAP        10.129.108.38   389    DC01             krbtgt                        2026-05-09 04:41:01 0        Key Distribution Center Service Account
LDAP        10.129.108.38   389    DC01             alex.turner                   2026-05-09 05:00:08 0
LDAP        10.129.108.38   389    DC01             ryan.brooks                   2026-05-10 09:46:18 0
LDAP        10.129.108.38   389    DC01             svc_deploy                    2026-05-09 05:01:19 0        Deployment service account
LDAP        10.129.108.38   389    DC01             james.harper                  2026-05-09 05:02:53 0
LDAP        10.129.108.38   389    DC01             sarah.mitchell                2026-05-09 05:02:58 0
LDAP        10.129.108.38   389    DC01             emily.carter                  2026-05-09 05:03:05 0
LDAP        10.129.108.38   389    DC01             david.reynolds                2026-05-09 05:03:11 0
LDAP        10.129.108.38   389    DC01             jessica.coleman               2026-05-09 05:03:15 0
LDAP        10.129.108.38   389    DC01             lauren.flores                 2026-05-09 05:03:21 0
LDAP        10.129.108.38   389    DC01             michael.torres                2026-05-09 05:03:28 0
LDAP        10.129.108.38   389    DC01             kevin.patterson               2026-05-09 05:03:33 0
LDAP        10.129.108.38   389    DC01             brian.jenkins                 2026-05-09 05:03:37 0
LDAP        10.129.108.38   389    DC01             megan.perry                   2026-05-09 05:03:42 0
LDAP        10.129.108.38   389    DC01             max.palmer                    2026-05-25 21:25:15 0


```