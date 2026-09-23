## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
File system analysis is a fundamental skill in digital forensics, allowing investigators to extract and interpret data stored on storage devices. EXT4, the default file system for many Linux distributions, organizes data into structures like inodes, directories, and blocks, each carrying critical information about files, directories, and their history.

This room focuses on analyzing the EXT4 file system to gather evidence such as file creation, deletion, and manipulation. We'll learn how to use native Linux tools and forensic software to detect anti-forensic techniques like timestomping, recover deleted files, and interpret file system metadata.

## Learning Objectives

- Learn about the file system EXT4 structure
- Recognize forensic artifacts of the EXT4 file system
- Analyze Timestamps and events
- Learn about tools to analyze the EXT4 file system
```

## Commands Useful For EXT 
`Lets First check the mnt directories` #EXT_lsblk
```bash
sudo lsblk
```
Then analyze the `super block` structure of the EXT Block
```c
struct ext4_super_block {
/*00*/  __le32  s_inodes_count;         /* Inodes count */
        __le32  s_blocks_count_lo;      /* Blocks count */
        __le32  s_r_blocks_count_lo;    /* Reserved blocks count */
        __le32  s_free_blocks_count_lo; /* Free blocks count */
/*10*/  __le32  s_free_inodes_count;    /* Free inodes count */
        __le32  s_first_data_block;     /* First Data Block */
        __le32  s_log_block_size;       /* Block size */
        __le32  s_log_cluster_size;     /* Allocation cluster size */
/*20*/  __le32  s_blocks_per_group;     /* # Blocks per group */
        __le32  s_clusters_per_group;   /* # Clusters per group */
        __le32  s_inodes_per_group;     /* # Inodes per group */
        __le32  s_mtime;                /* Mount time */
/*30*/  __le32  s_wtime;                /* Write time */
        __le16  s_mnt_count;            /* Mount count */
        __le16  s_max_mnt_count;        /* Maximal mount count */
        __le16  s_magic;                /* Magic signature */
        __le16  s_state;                /* File system state */
        __le16  s_errors;               /* Behaviour when detecting errors */
        __le16  s_minor_rev_level;      /* minor revision level */
/*40*/  __le32  s_lastcheck;            /* time of last check */
        __le32  s_checkinterval;        /* max. time between checks */
        __le32  s_creator_os;           /* OS */
        __le32  s_rev_level;            /* Revision level */
/*50*/  __le16  s_def_resuid;           /* Default uid for reserved blocks */
        __le16  s_def_resgid;           /* Default gid for reserved blocks */
    (...)
};
```
`Now Let's talk about dd Command` #EXT_dd
```bash
sudo dd if=/dev/loop0 bs=1024 skip=1 count=1 | hexdump -C 
```
`Next Command is dumpe2fs` #EXT_dumpe2fs
```bash
sudo dumpe2fs /dev/loop0
```
`There is one another command which will come handy that is debugfs` #EXT_debugfs
```bash
sudo debugfs /dev/loop0
```

## Forensic Artifacts in EXT 
#EXT_Forensic
```Description
The EXT file system, which is used in Linux-based systems, contains a lot of information that can be helpful during an investigation. This is because by knowing the basic structure of EXT, which includes inodes, metadata, journaling, and data block management, we are able to retrieve essential information regarding files and directories, including the history of access, modification, and deletion.  
Some of the features that have been incorporated in the EXT3 and extended into EXT4 include journaling. This helps in the prevention of corruption by making a list of changes that are to be made on the disk recently. This is why they are both targets of a forensic investigation. As we will learn, journals of deleted files can have metadata, events, and data that can be processed or recovered. In this task, we'll walk through practical steps to locate, extract, and analyze these artifacts, and we'll understand how to find and interpret the critical forensic artifacts of EXT file systems. We'll also enumerate them, examine some examples, and explore how attackers can modify or tamper with the information contained within them.
```
## Inode Metadata
```c
struct ext4_inode {
    __le16  i_mode;              /* File mode (permissions + type) */
    __le16  i_uid;               /* Low 16 bits of Owner UID */
    __le32  i_size_lo;           /* Lower 32 bits of file size in bytes */
    __le32  i_atime;             /* Last access time */
    __le32  i_ctime;             /* Inode change time */
    __le32  i_mtime;             /* Last modification time */
    __le32  i_dtime;             /* Deletion time (if unlinked) */
    __le16  i_gid;               /* Low 16 bits of Group ID */
    __le16  i_links_count;       /* Hard links count */
    __le32  i_blocks_lo;         /* Lower 32 bits of block count (in 512-byte sectors) */
    __le32  i_flags;             /* File flags (e.g., immutable, append-only) */
    union {
        struct {
            __le32  l_i_version; /* File version (used by Linux) */
        } linux1;
        struct {
            __u32   h_i_translator; /* HURD-specific field */
        } hurd1;
        struct {
            __u32   m_i_reserved1;  /* MASIX-specific field */
        } masix1;
    } osd1;                       /* OS-dependent 1 */
    __le32  i_block[EXT4_N_BLOCKS]; /* Block pointers (direct, indirect, etc.) */
    __le32  i_generation;         /* File version for NFS */
    __le32  i_file_acl_lo;        /* Lower 32 bits of file ACL */
    __le32  i_size_high;          /* Upper 32 bits of file size (for >4GB files) */
    __le32  i_obso_faddr;         /* Obsolete fragment address (unused in EXT4) */
    union {
        struct {
            __le16  l_i_blocks_high;     /* Upper 16 bits of block count */
            __le16  l_i_file_acl_high;   /* Upper 16 bits of file ACL */
            __le16  l_i_uid_high;        /* Upper 16 bits of UID */
            __le16  l_i_gid_high;        /* Upper 16 bits of GID */
            __le16  l_i_checksum_lo;     /* Checksum (CRC32C UUID+inum+inode) LE */
            __le16  l_i_reserved;        /* Reserved */
        } linux2;
        struct {
            __le16  h_i_reserved1;       /* Obsolete in EXT4 */
            __u16   h_i_mode_high;
            __u16   h_i_uid_high;
            __u16   h_i_gid_high;
            __u32   h_i_author;
        } hurd2;
        struct {
            __le16  h_i_reserved1;
            __le16  m_i_file_acl_high;
            __u32   m_i_reserved2[2];
        } masix2;
    } osd2;                      /* OS-dependent 2 */
    __le16  i_extra_isize;       /* Size of extended inode fields */
    __le16  i_checksum_hi;       /* Checksum high bits (CRC32C, big-endian) */
    __le32  i_ctime_extra;       /* Extra inode change time (nsec & epoch) */
    __le32  i_mtime_extra;       /* Extra modification time (nsec & epoch) */
    __le32  i_atime_extra;       /* Extra access time (nsec & epoch) */
    __le32  i_crtime;            /* File creation time */
    __le32  i_crtime_extra;      /* Extra file creation time (nsec & epoch) */
    __le32  i_version_hi;        /* Upper 32 bits of file version */
    __le32  i_projid;            /* Project ID (Quota & project support) */
};
```

## Command 
For surfing obvious #EXT_stat
```bash
ls -al /mnt/ext4_partition
sudo stat <file_name.txt>
```

## Check MNT dev
```bash
ubuntu@tryhackme:~$ df -T
Filesystem     Type  1K-blocks     Used Available Use% Mounted on
/dev/root      ext4   60910776 13523444  47370948  23% /
tmpfs          tmpfs   1980696        0   1980696   0% /dev/shm
tmpfs          tmpfs    792280     1188    791092   1% /run
tmpfs          tmpfs      5120        0      5120   0% /run/lock
/dev/loop0     ext4      91840       20     84652   1% /mnt/ext4_partition
/dev/loop1     ext4      91840       28     84644   1% /mnt/ext_exercises
tmpfs          tmpfs    396136      196    395940   1% /run/user/1000
tmpfs          tmpfs    396136      172    395964   1% /run/user/114
```

Process of Recovering File 
`sudo strings -d t /dev/loop0 | grep -i "AAAAAAAA`
output will be like `100671488 AAAAAAAAA`

then we need to print the memory location of the file 
```bash
echo $((100671488 / 4096))
output will be 24578 //location in memory
```

then for recovery of the file run command 
```bash 
sudo dd if=/dev/loop0 bs=4096 skip=24578 count=1 of=/tmp/recovered_File
```
 then in practical we need to take out the or you can say we need to recovered the file so this is lets first on first question which is 
 `Identify the timestomped file in the mounted file system in `/mnt/ext_exercises`. What is the original creation date of the file? (Format: YYYY-MM-DD hh:mm:ss)`
This is actually easy simply run 
```bash 
ubuntu@tryhackme:/mnt/ext_exercises$ cd
ubuntu@tryhackme:~$ cd /mnt/ext_exercises/
ubuntu@tryhackme:/mnt/ext_exercises$ ls -la
total 28
drwxrwxrwx 3 root    root     4096 Jan  9  2025 .
drwxr-xr-x 8 root    root     4096 Jan  9  2025 ..
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 01_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 02_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 03_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 04_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 05_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 06_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 07_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 08_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 09_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 10_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 11_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 12_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 13_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 14_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 15_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 16_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  1  2020 17_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 18_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 19_file.txt
-rw-rw-r-- 1 analyst analyst     0 Jan  9  2025 20_file.txt
drwxrwxrwx 2 root    root    16384 Jan  9  2025 lost+found
-rw-rw-r-- 1 analyst analyst    23 Jan  9  2025 normal_file.txt
//notice any difference between file and i found out simple creation dat eamount the files 
```
which is file `17_file.txt`
so next we simply run stat 
```bash
ubuntu@tryhackme:/mnt/ext_exercises$ sudo stat 17_file.txt 
  File: 17_file.txt
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: 7,1	Inode: 29          Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1001/ analyst)   Gid: ( 1001/ analyst)
Access: 2020-01-01 12:00:00.000000000 +0000
Modify: 2020-01-01 12:00:00.000000000 +0000
Change: 2025-01-09 02:30:40.101440929 +0000
 Birth: 2025-01-09 02:27:53.338604519 +0000
```
Got the gate which is `2025-01-09 02:27:53`
and now we need to check and recovered the deleted file but first check the dev owner for the mnt directory which we already did above which is `/dev/loop1` so lets start

```bash
ubuntu@tryhackme:~$ sudo strings -t d /dev/loop1 | grep -i "FFFFFFFFFF"
100667392 FFFFFFFFFF
ubuntu@tryhackme:~$ echo $((100667392 /4096))
24577
ubuntu@tryhackme:~$ sudo dd if=/dev/loop1 bs=4096 skip=24577 count=1 of=/tmp/recovered_file
1+0 records in
1+0 records out
4096 bytes (4.1 kB, 4.0 KiB) copied, 9.627e-05 s, 42.5 MB/s
ubuntu@tryhackme:~$ cat /tmp/recovered_file 
FFFFFFFFFF
XXXXXXXX
YYYYYYYY
[flag omitted]
ubuntu@tryhackme:~$ 
```

## Flag
```flag
[flag omitted]
```