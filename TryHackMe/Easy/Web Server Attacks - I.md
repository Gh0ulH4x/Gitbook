## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Enumerate and identify misconfigurations across Apache, Nginx, Node.js, and Python HTTP Server.
```
## IP-Address
```IP-Address
10.48.187.77
```
----
#### Enumeration 
- Scan
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.5 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    syn-ack ttl 62 Apache httpd 2.4.58 ((Ubuntu))
| http-enum:
|   /files/: Potentially interesting directory w/ listing on 'apache/2.4.58 (ubuntu)'
|_  /server-status/: Potentially interesting folder
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-wordpress-users: [Error] Wordpress installation was not found. We couldn't find wp-login.php
|_http-litespeed-sourcecode-download: Request with null byte did not work. This web server might not be vulnerable
|_http-server-header: Apache/2.4.58 (Ubuntu)
|_http-jsonp-detection: Couldn't find any JSONP endpoints.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
3000/tcp open  http    syn-ack ttl 62 Node.js Express framework
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-wordpress-users: [Error] Wordpress installation was not found. We couldn't find wp-login.php
|_http-jsonp-detection: Couldn't find any JSONP endpoints.
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-litespeed-sourcecode-download: Request with null byte did not work. This web server might not be vulnerable
8000/tcp open  http    syn-ack ttl 62 SimpleHTTPServer 0.6 (Python 3.12.3)
|_http-server-header: SimpleHTTP/0.6 Python/3.12.3
|_http-iis-webdav-vuln: WebDAV is DISABLED. Server is not currently vulnerable.
| http-enum:
|_  /backup.zip: Possible backup
|_http-wordpress-users: [Error] Wordpress installation was not found. We couldn't find wp-login.php
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-jsonp-detection: Couldn't find any JSONP endpoints.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-csrf: Couldn't find any CSRF vulnerabilities.
8080/tcp open  http    syn-ack ttl 62 nginx 1.24.0 (Ubuntu)
| http-vuln-cve2011-3192:
|   VULNERABLE:
|   Apache byterange filter DoS
|     State: VULNERABLE
|     IDs:  CVE:CVE-2011-3192  BID:49303
|       The Apache web server is vulnerable to a denial of service attack when numerous
|       overlapping byte ranges are requested.
|     Disclosure date: 2011-08-19
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-3192
|       https://seclists.org/fulldisclosure/2011/Aug/175
|       https://www.tenable.com/plugins/nessus/55976
|_      https://www.securityfocus.com/bid/49303
|_http-server-header: nginx/1.24.0 (Ubuntu)
|_http-jsonp-detection: Couldn't find any JSONP endpoints.
|_http-litespeed-sourcecode-download: Request with null byte did not work. This web server might not be vulnerable
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-wordpress-users: [Error] Wordpress installation was not found. We couldn't find wp-login.php
| http-enum:
|_  /files/: Potentially interesting folder w/ directory listing
|_http-csrf: Couldn't find any CSRF vulnerabilities.
```
- Services
```bash
$ curl -sI http://10.48.187.77:3000/
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 56
ETag: W/"38-K8iCfm09rMr0MV0NsgqdAb94DAk"
Date: Tue, 12 May 2026 07:59:24 GMT
Connection: keep-alive
Keep-Alive: timeout=5

$ curl -sI http://10.48.187.77:8080/
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Tue, 12 May 2026 07:56:46 GMT
Content-Type: text/html
Content-Length: 136
Last-Modified: Fri, 03 Apr 2026 18:23:00 GMT
Connection: keep-alive
ETag: "69d00584-88"
Accept-Ranges: bytes

$ curl -sI http://10.48.187.77:8000/
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.12.3
Date: Tue, 12 May 2026 07:58:57 GMT
Content-type: text/html; charset=utf-8
Content-Length: 353
```
----
### Web Application
- Port 8000
```bash
- Web App
.env
backup.zip
config.txt
notes.txt

- Config.txt
db_host=localhost
db_user=webapp
db_password=OldP@ssw0rd99
db_name=staging

- notes.txt
TODO: remove this before going live
Admin creds: admin / admin123
Staging server: 10.10.100.50

- env
SECRET_KEY=dev-secret-key-do-not-use
DATABASE_URL=postgresql://webapp:S3cur3DBPass!@localhost/production
DEBUG=True

- backup.zip
$ unzip backup.zip
Archive:  backup.zip
  inflating: db_dump.sql

$ cat db_dump.sql
-- Database dump for staging environment
-- Generated: 2024-11-01

CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    created_at TIMESTAMP
);

INSERT INTO users VALUES (1, 'admin', 'admin@company.com', '2024-01-01 09:00:00');
INSERT INTO users VALUES (2, 'jsmith', 'jsmith@company.com', '2024-03-15 14:30:00');
INSERT INTO users VALUES (3, 'jdoe', 'jdoe@company.com', '2024-06-22 11:00:00');

CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10,2)
);

INSERT INTO products VALUES (1, 'Widget A', 9.99);
INSERT INTO products VALUES (2, 'Widget B', 19.99);

-- End of dump
-- flag: [flag omitted]
```
- Port 80

```bash
 feroxbuster -u http://10.48.187.77/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
http://10.48.187.77/icons/ubuntu-logo.png
http://10.48.187.77/
http://10.48.187.77/files => http://10.48.187.77/files/
http://10.48.187.77/files/internal-notes.txt
http://10.48.187.77/files/employees.csv
http://10.48.187.77/javascript => http://10.48.187.77/javascript/
http://10.48.187.77/javascript/async => http://10.48.187.77/javascript/async/
http://10.48.187.77/javascript/async/async
http://10.48.187.77/server-status
http://10.48.187.77/javascript/events/
http://10.48.187.77/javascript/events/events
http://10.48.187.77/javascript/util => http://10.48.187.77/javascript/util/
http://10.48.187.77/javascript/util/support => http://10.48.187.77/javascript/util/support/
http://10.48.187.77/javascript/util/support/isBuffer
http://10.48.187.77/javascript/util/support/types
http://10.48.187.77/javascript/util/util
http://10.48.187.77/
http://10.48.187.77/files/
http://10.48.187.77/javascript/
http://10.48.187.77/javascript/async/
http://10.48.187.77/javascript/events/
http://10.48.187.77/javascript/util/
http://10.48.187.77/javascript/util/support/ 
```
- Inside Files Directory
```bash
employees.csv	
internal-notes.txt	
```
- Files Content
```bash
- Internal-Notes.txt
Meeting notes - Q3 review
Server migration date: 2026-05-01
Action items: update firewall rules, rotate API keys
flag: [flag omitted]

$ cat employees.csv
id,name,email
1,John Smith,jsmith@company.com
2,Jane Doe,jdoe@company.com
3,Admin User,admin@company.com
```
- Port 3000
```bash
http://10.48.187.77:3000/static/config.js
http://10.48.187.77:3000/api/debug/env
http://10.48.187.77:3000/api/routes
http://10.48.187.77:3000/api/Users

Users
{"error":"connect ECONNREFUSED 127.0.0.1:5432","stack":"Error: connect ECONNREFUSED 127.0.0.1:5432\n    at /opt/nodeapp/app.js:16:15\n    at Layer.handle [as handle_request] (/opt/nodeapp/node_modules/express/lib/router/layer.js:95:5)\n    at next (/opt/nodeapp/node_modules/express/lib/router/route.js:149:13)\n    at Route.dispatch (/opt/nodeapp/node_modules/express/lib/router/route.js:119:3)\n    at Layer.handle [as handle_request] (/opt/nodeapp/node_modules/express/lib/router/layer.js:95:5)\n    at /opt/nodeapp/node_modules/express/lib/router/index.js:284:15\n    at Function.process_params (/opt/nodeapp/node_modules/express/lib/router/index.js:346:12)\n    at next (/opt/nodeapp/node_modules/express/lib/router/index.js:280:10)\n    at expressInit (/opt/nodeapp/node_modules/express/lib/middleware/init.js:40:5)\n    at Layer.handle [as handle_request] (/opt/nodeapp/node_modules/express/lib/router/layer.js:95:5)","query":"SELECT * FROM users"}

routes
[{"method":"GET","path":"/"},{"method":"GET","path":"/api/users"},{"method":"GET","path":"/api/routes"},{"method":"GET","path":"/api/debug/env"}]

env
{"NODE_ENV":"development","DB_PASSWORD":"NodeDBPass2024!","PORT":"3000","DB_HOST":"localhost:5432","APP_NAME":"company-portal"}

config.js
// Client-side configuration
const API_BASE = 'http://internal-api.company.local:8080';
const DEBUG = true;
const VERSION = '1.2.0';
// flag: [flag omitted]
```
- Port 8000
```bash
- 
http://10.48.187.77:8080/files/deploy-notes.txt
http://10.48.187.77:8080/files/server-config.txt
http://10.48.187.77:8080/files/old-backup.tar.gz

deploy-notes.txt
Deployment runbook v3
SSH key location: /home/deploy/.ssh/id_rsa
Sudo password: [password omitted]
Last deployed: 2024-10-30
Contact: ops@company.internal

server-config.txt
server_name: web01.company.internal
internal_ip: 10.10.100.50
admin_port: 8443
backup_schedule: daily 02:00
flag: [flag omitted]

old-backup.tar.gz
tar -xf old-backup.tar.gz
index.html
old<404>page
```
----
- Last `Nikto` on Port 80 
```bash
$  nikto -h http://10.48.187.77:80 -nointeractive
- Nikto v2.6.0
---------------------------------------------------------------------------
+ Target IP:          10.48.187.77
+ Target Hostname:    10.48.187.77
+ Target Port:        80
+ Platform:           Linux/Unix
+ Start Time:         2026-05-12 04:38:47 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.58 (Ubuntu)
+ ERROR: Failed to check for updates: 403
+ No CGI Directories found (use '-C all' to force check all possible dirs). CGI tests skipped.
+ [600050] Apache/2.4.58 appears to be outdated (current is at least 2.4.66).
+ [999984] /: Server may leak inodes via ETags, header found with file /, inode: 29af, size: 64e9243796aa2, mtime: gzip. See: https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ [013587] /: Suggested security header missing: referrer-policy. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy
+ [013587] /: Suggested security header missing: x-content-type-options. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options
+ [013587] /: Suggested security header missing: strict-transport-security. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
+ [013587] /: Suggested security header missing: content-security-policy. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP
+ [013587] /: Suggested security header missing: permissions-policy. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Permissions-Policy
+ [999990] OPTIONS: Allowed HTTP Methods: GET, POST, OPTIONS, HEAD .
+ [001406] /server-status: The mod_status module reveals Apache information. See: https://httpd.apache.org/docs/2.4/mod/mod_status.html
+ [750500] /files/: Directory indexing found.
+ [001669] /files/: This might be interesting.
+ [750500] /files/: Directory indexing found.
+ No CGI Directories found (use '-C all' to force check all possible dirs). CGI tests skipped.
+ [600050] Apache/2.4.58 appears to be outdated (current is at least 2.4.66).
+ [013587] /files/: Suggested security header missing: x-content-type-options. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options
+ [013587] /files/: Suggested security header missing: permissions-policy. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Permissions-Policy
+ [013587] /files/: Suggested security header missing: referrer-policy. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy
+ [013587] /files/: Suggested security header missing: strict-transport-security. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
+ [013587] /files/: Suggested security header missing: content-security-policy. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP
+ [999990] OPTIONS: Allowed HTTP Methods: GET, POST, OPTIONS, HEAD .
+ [750500] /files/./: Directory indexing found.
+ [000327] /files/./: Appending '/./' to a directory allows indexing.
+ [750500] /files//: Directory indexing found.
+ [000396] /files//: Apache on Red Hat Linux release 9 reveals the root directory listing by default if there is no index page.
+ [750500] /files/%2e/: Directory indexing found.
+ [000460] /files/%2e/: Weblogic allows source code or directory listing, upgrade to v6.0 SP1 or higher. See: https://web.archive.org/web/20171102042459/http://www.securityfocus.com/bid/2513
+ [750500] /files///: Directory indexing found.
```
# END

