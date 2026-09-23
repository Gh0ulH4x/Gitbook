# Level 14 → Level 15

## Description
```Description
## Level Goal
The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.
## Commands you may need to solve this level

ssh, telnet, nc, openssl, s_client, nmap

## Helpful Reading Material

- [How the Internet works in 5 minutes (YouTube)](https://www.youtube.com/watch?v=7_LPdttKXPc) (Not completely accurate, but good enough for beginners)
- [IP Addresses](https://computer.howstuffworks.com/web-server5.htm)
- [IP Address on Wikipedia](https://en.wikipedia.org/wiki/IP_address)
- [Localhost on Wikipedia](https://en.wikipedia.org/wiki/Localhost)
- [Ports](https://computer.howstuffworks.com/web-server8.htm)
- [Port (computer networking) on Wikipedia](https://en.wikipedia.org/wiki/Port_\(computer_networking\))
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit14
Password: MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

## Let's Explore
```bash
bandit14@bandit:~$ ifconfig
ens5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 10.0.1.59  netmask 255.255.255.0  broadcast 10.0.1.255
        inet6 fe80::49c:13ff:fe2a:ed15  prefixlen 64  scopeid 0x20<link>
        ether 06:9c:13:2a:ed:15  txqueuelen 1000  (Ethernet)
        RX packets 471863759  bytes 40869437049 (40.8 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 522919018  bytes 116327307386 (116.3 GB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 99186726  bytes 10032664241 (10.0 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 99186726  bytes 10032664241 (10.0 GB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

bandit14@bandit:~$ nmap -A 10.0.1.59
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-11 23:19 UTC
Stats: 0:00:36 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 42.86% done; ETC: 23:21 (0:00:31 remaining)
Stats: 0:01:19 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 57.14% done; ETC: 23:22 (0:00:50 remaining)
Stats: 0:01:44 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 57.14% done; ETC: 23:22 (0:01:08 remaining)
Nmap scan report for 10.0.1.59
Host is up (0.00011s latency).
Not shown: 993 closed tcp ports (conn-refused)
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 9.6p1 (protocol 2.0)
| ssh-hostkey: 
|   256 cf:9f:ed:ce:5e:fc:24:f5:57:14:a8:01:65:b2:3c:f1 (ECDSA)
|_  256 d6:22:3d:39:3b:5c:c9:18:96:c6:8d:26:2a:91:e3:63 (ED25519)
1111/tcp  open  time       (32 bits)
3000/tcp  open  ppp?
| fingerprint-strings: 
|   GenericLines, GetRequest, NULL: 
|_    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
8000/tcp  open  http-alt?
12345/tcp open  tcpwrapped
30000/tcp open  ndmps?
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, LPDString, RTSPRequest, SIPOptions: 
|_    Wrong! Please enter the correct current password.
50001/tcp open  unknown
| fingerprint-strings: 
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, LPDString, RTSPRequest, SIPOptions: 
|_    Wrong! Please enter the correct current password.
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port3000-TCP:V=7.94SVN%I=7%D=8/11%Time=689A7AAA%P=x86_64-pc-linux-gnu%r
SF:(NULL,21,"0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO\n")%r(GenericLines,21,"0qXah
SF:G8ZjOVMN9Ghs7iOWsCfZyXOUbYO\n")%r(GetRequest,21,"0qXahG8ZjOVMN9Ghs7iOWs
SF:CfZyXOUbYO\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port30000-TCP:V=7.94SVN%I=7%D=8/11%Time=689A7AB0%P=x86_64-pc-linux-gnu%
SF:r(GenericLines,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20curre
SF:nt\x20password\.\n")%r(GetRequest,32,"Wrong!\x20Please\x20enter\x20the\
SF:x20correct\x20current\x20password\.\n")%r(HTTPOptions,32,"Wrong!\x20Ple
SF:ase\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(RTSPReque
SF:st,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20passwo
SF:rd\.\n")%r(Help,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20curr
SF:ent\x20password\.\n")%r(FourOhFourRequest,32,"Wrong!\x20Please\x20enter
SF:\x20the\x20correct\x20current\x20password\.\n")%r(LPDString,32,"Wrong!\
SF:x20Please\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(SIP
SF:Options,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20p
SF:assword\.\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port50001-TCP:V=7.94SVN%I=7%D=8/11%Time=689A7AB5%P=x86_64-pc-linux-gnu%
SF:r(GenericLines,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20curre
SF:nt\x20password\.\n")%r(GetRequest,32,"Wrong!\x20Please\x20enter\x20the\
SF:x20correct\x20current\x20password\.\n")%r(HTTPOptions,32,"Wrong!\x20Ple
SF:ase\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(RTSPReque
SF:st,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20passwo
SF:rd\.\n")%r(Help,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20curr
SF:ent\x20password\.\n")%r(FourOhFourRequest,32,"Wrong!\x20Please\x20enter
SF:\x20the\x20correct\x20current\x20password\.\n")%r(LPDString,32,"Wrong!\
SF:x20Please\x20enter\x20the\x20correct\x20current\x20password\.\n")%r(SIP
SF:Options,32,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20p
SF:assword\.\n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 165.83 seconds
```

After this we know we have open ports present on localhost running some sort of services
but before that we need to get the user password of bandit14
```bash
bandit14@bandit:~$ ls
bandit14@bandit:~$ cd /etc/bandit_pass/
bandit14@bandit:/etc/bandit_pass$ ls
bandit0   bandit12  bandit16  bandit2   bandit23  bandit27  bandit30  bandit4  bandit8
bandit1   bandit13  bandit17  bandit20  bandit24  bandit28  bandit31  bandit5  bandit9
bandit10  bandit14  bandit18  bandit21  bandit25  bandit29  bandit32  bandit6
bandit11  bandit15  bandit19  bandit22  bandit26  bandit3   bandit33  bandit7
bandit14@bandit:/etc/bandit_pass$ cat bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

Got the Password for the bandit14 so now lets work on to retrieve bandit15 password.
```bash
bandit14@bandit:~$ echo "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS" | nc localhost 30000
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```
