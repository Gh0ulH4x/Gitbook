## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Find what's hidden deep inside this website.
My Dearest Hacker,
Cupid's Vault was designed to protect secrets meant to stay hidden forever. Unfortunately, Cupid underestimated how determined attackers can be.
Intelligence indicates that Cupid may have unintentionally left vulnerabilities in the system. With the holiday deadline approaching, you've been tasked with uncovering what's hidden inside the vault before it's too late.
You can find the web application here: `http://10.48.168.120:5000`
```
## IP-Address
```IP-Address
http://10.48.168.120:5000
```
## Gobuster 
```bash
 gobuster dir \
-u http://10.48.168.120:5000/ \
-w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt \
-x php,html,txt \
-t 50
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.48.168.120:5000/
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              php,html,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/console              (Status: 400) [Size: 167]
/robots.txt           (Status: 200) [Size: 70]
/robots.txt           (Status: 200) [Size: 70]
Progress: 18984 / 18984 (100.00%)
===============================================================
Finished
===============================================================
```
## Robots.txt
```html
User-agent: *
Disallow: /cupids_secret_vault/*

# [password omitted]
```
## Gobuster 
```bash
└─$ gobuster dir \
-u http://10.48.168.120:5000/cupids_secret_vault/ \
-w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt \
-x php,html,txt \
-t 50
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.48.168.120:5000/cupids_secret_vault/
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              php,html,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/administrator        (Status: 200) [Size: 2381]
Progress: 18984 / 18984 (100.00%)
===============================================================
Finished
===============================================================
```
## Administrator
- Login Page 
```bash
Login page Creds are
Username : admin 
Password : [password omitted]
```
## Flag
```Flag
[flag omitted] 
```

# END