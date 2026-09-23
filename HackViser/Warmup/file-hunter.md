## Description
```Description
FTP (File Transfer Protocol) is a protocol used to make file transfers over the internet. This protocol is used to upload or download files from one computer to another computer.  
  
Recommended for practicing the basics of the FTP service.
```

```IP-Address
172.20.2.85
```
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

## Rustscan 
#rustscan
```bash
 rustscan -a 172.20.2.85 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.38 seconds
           Raw packets sent: 1 (44B) | Rcvd: 1 (44B)
```

## FTP Surf
```bash 
ftp 172.20.2.85
Connected to 172.20.2.85.
220 Welcome to anonymous Hackviser FTP service.
Name (172.20.2.85:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||56045|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            25 Sep 08  2023 userlist
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||53685|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Sep 08  2023 .
drwxr-xr-x    2 ftp      ftp          4096 Sep 08  2023 ..
-rw-r--r--    1 ftp      ftp            25 Sep 08  2023 userlist
226 Directory send OK.
ftp> get userlist
local: userlist remote: userlist
229 Entering Extended Passive Mode (|||8426|)
150 Opening BINARY mode data connection for userlist (25 bytes).
100% |*******************************************************************|    25       47.58 KiB/s    00:00 ETA
226 Transfer complete.
```

## Flag
```bash
┌──(kali㉿kali)-[~]
└─$ cat userlist 
[flag content omitted]
```