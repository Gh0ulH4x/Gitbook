## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
SSH (Secure Shell) is a protocol used to securely access and manage devices on a network. It encrypts data to protect confidentiality and integrity, making SSH a preferred option over Telnet for remote administration.  
  
Recommended for practicing the basics of the SSH service.
```

## IP-Address
```Ip-Address
172.20.2.50
```

## RustScan
```bash
rustscan -a 172.20.2.50 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 14.31 seconds
           Raw packets sent: 2 (88B) | Rcvd: 1 (44B)
```

## SSH
```bash
ssh hackviser@172.20.2.50
--------------------------------------
Secure Command
--------------------------------------

Master's Message: W3lc0m3 t0 h4ck1ng w0rld⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
hackviser@172.20.2.50's password: [password omitted]
```

## Explore Linux Machine
```bash
hackviser@secure-command:~$ ls 
hackviser@secure-command:~$ id
uid=1000(hackviser) gid=1000(hackviser) groups=1000(hackviser),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),100(users),106(netdev)
hackviser@secure-command:~$ pwd
/home/hackviser
hackviser@secure-command:~$ sudo su
-bash: sudo: command not found
hackviser@secure-command:~$ su
Password: 
root@secure-command:/home/hackviser# 
```

## Flag
```bash
root@secure-command:~# ls -la
total 24
drwx------  4 root root 4096 Jul 20 01:16 .
drwxr-xr-x 18 root root 4096 Sep 12  2023 ..
-rw-r--r--  1 root root   13 Nov 18  2023 .advice_of_the_master
-rw-r--r--  1 root root  697 Nov 18  2023 .bashrc
drwxr-xr-x  3 root root 4096 Nov 18  2023 .local
drwx------  2 root root 4096 Jul 20 01:09 .ssh
root@secure-command:~# ls -a
.  ..  .advice_of_the_master  .bashrc  .local  .ssh
root@secure-command:~# cat .advice_of_the_master 
[flag content omitted]
root@secure-command:~# 
```