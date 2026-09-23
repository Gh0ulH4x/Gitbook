## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Cipher’s legion of bots has exploited a known vulnerability in our web application, leaving behind a dangerous web shell implant. Investigate the breach and trace the attacker's footsteps!

Note: Click the **Start Machine** button to spawn the Virtual Machine.
```

## Lets Find check the files
Directory - `/var/www/html/CMS-Master/` and check for profile.php which reflect the file injection critical vulnerability which allow hacker to upload an php encoded Image into the server at `/var/www/html/CMS-Master/img` now lets find out about him 

## Access Logs 
`/var/log/apache2/` in this directory we find Files like
```bash
ubuntu@tryhackme:/var/log/apache2$ ls -la
total 56
drwxr-x---  2 root adm     4096 Jul 27 17:06 .
drwxrwxr-x 20 root syslog  4096 Jul 27 17:06 ..
-rw-r-----  1 root adm        0 Mar  6 09:32 access.log
-rw-r-----  1 root adm        0 Jul 27 17:06 error.log
-rw-r-----  1 root adm     4189 Mar  6 15:55 error.log.1
-rw-r-----  1 root adm        0 Jul 27 17:06 other_vhosts_access.log
-rw-r-----  1 root adm    33908 Mar  6 10:11 other_vhosts_access.log.1
```
as we can see only 2 files contain data where access.log.1 is confirmed not cipher as written inside so in other must be attacker

```bash
$ cat other_vhosts_access.log.1  | grep images
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:47:51 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 491 "http://10.10.80.94:8080/CMSsite-master/admin/profile.php?section=not_cipher" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:48:00 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 492 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:48:01 +0000] "GET /favicon.ico HTTP/1.1" 404 491 "http://10.10.80.94:8080/CMSsite-master/img/images.php" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:48:14 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 491 "http://10.10.80.94:8080/CMSsite-master/admin/profile.php?section=not_cipher" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:48:17 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 491 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:48:22 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 491 "http://10.10.80.94:8080/CMSsite-master/admin/profile.php?section=not_cipher" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:48:24 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 491 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:49:10 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 404 491 "http://10.10.80.94:8080/CMSsite-master/admin/profile.php?section=not_cipher" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:50:33 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 500 185 "http://10.10.80.94:8080/CMSsite-master/admin/profile.php?section=not_cipher" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:50:41 +0000] "GET /CMSsite-master/img/images.php HTTP/1.1" 500 185 "http://10.10.80.94:8080/CMSsite-master/img/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:50:57 +0000] "GET /CMSsite-master/img/images.php?query=d2hvYW1pCg== HTTP/1.1" 200 212 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:51:11 +0000] "GET /CMSsite-master/img/images.php?query=bHMK HTTP/1.1" 200 660 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:51:20 +0000] "GET /CMSsite-master/img/images.php?query=ZWNobyAnVEhNe3N1cDNyXzM0c3lfdzNic2gzbGx9Jwo= HTTP/1.1" 200 229 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:51:28 +0000] "GET /CMSsite-master/img/images.php?query=aWZjb25maWcK HTTP/1.1" 200 203 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:51:40 +0000] "GET /CMSsite-master/img/images.php?query=Y2F0IC9ldGMvcGFzc3dkCg== HTTP/1.1" 200 1546 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
ip-10-10-80-94.eu-west-1.compute.internal:80 10.11.93.143 - - [06/Mar/2025:09:51:47 +0000] "GET /CMSsite-master/img/images.php?query=aWQK HTTP/1.1" 200 258 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
```

And in this log we find out base64 encoded logs like 
```base64
d2hvYW1pCg==
bHMK
ZWNobyAnVEhNe3N1cDNyXzM0c3lfdzNic2gzbGx9Jwo=
```
Output
```plain
whoami
ls
echo '[flag omitted]'
```
Got the flag 
```Flag
[flag omitted]
```