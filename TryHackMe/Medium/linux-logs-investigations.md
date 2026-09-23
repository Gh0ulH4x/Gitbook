## Description
```Description
Just like a map guides explorers, logs can be used to navigate system administrators and security analysts through the intricate world of Linux. This room equips you with essential skills to decipher these logs, focusing on core areas like logging levels, kernel whispers via `/var/log/kern.log`, user interactions, the watchful eye of auditd, the versatile syslog, and the modern journal. By the end, you'll be able to unearth hidden clues and safeguard your systems.

### Objectives

- Learn about the different types of logs recorded on Linux systems.
- Learn how to perform forensic analysis through logs on Linux systems, focused on determining malicious processes, services, and scripts.
- Hunt malicious processes, services, and configurations to mitigate further compromise in a hands-on IR scenario.
```

## Overview
```Overview
In the world of Linux systems administration, understanding logs is akin to deciphering the language of your server's soul. Logs provide a breadcrumb trail of system activities, errors, and events crucial for troubleshooting, auditing, and security analysis. Knowing how to locate, parse, and analyse log files is worth mastering.

The initial point is to understand the two primary types of logging mechanisms: kernel and user.

- **Kernel** logs provide a backstage pass into your system's inner workings. They include messages related to hardware events, driver operations, and system errors.
- **User** logs capture user interactions between users, applications, and the operating system. They include login attempts, command executions, and app-specific activities.
```


## Levels of errors 
| **Level**     | **Description**                                     |
| ------------- | --------------------------------------------------- |
| **EMERGENCY** | System is unusable or has crashed.                  |
| **ALERT**     | Immediate attention needed.                         |
| **CRITICAL**  | Serious hardware or software issue.                 |
| **ERROR**     | Non-critical error (e.g. device or driver failure). |
| **WARNING**   | Potential issue, but not urgent.                    |
| **NOTICE**    | Unusual events worth noting.                        |
| **INFO**      | General system activity and updates.                |
| **DEBUG**     | Detailed info for development and troubleshooting.  |
#Log-Commands
## 📝 Key Linux Log Files for DFIR
| **Log File**            | **Purpose**                                          | **How to Use in DFIR**                                                                   |
| ----------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **`/var/log/kern.log`** | Logs kernel messages                                 | Detect hardware failures, kernel-level exploits (e.g. rootkits), and module injections.  |
| **`/var/log/dmesg`**    | Boot-time kernel messages                            | Identify unusual system startup behavior or tampering (e.g. with `dmesg -T` and `grep`). |
| **`/var/log/auth.log`** | Tracks authentication attempts, sudo use, SSH logins | Investigate unauthorized access, brute-force attempts, and privilege escalation.         |
| **`/var/log/syslog`**   | General system activity log                          | Catch-all log for cron jobs, kernel messages, and overall system health checks.          |
| **`/var/log/wtmp`**     | Records all login and logout activities              | Use `last -f /var/log/wtmp` to trace user sessions and spot anomalies.                   |
| **`/var/log/btmp`**     | Logs failed login attempts                           | Detect brute-force attacks using tools like `lastb` or `utmpdump`.                       |
#DFIR-Commands
## 🔍 DFIR Examples & Commands
|**Log**|**DFIR Command Example**|**What It Reveals**|
|---|---|---|
|`kern.log`|`sudo tail -f /var/log/kern.log`|Kernel module activity, e.g., rootkit loading|
|`dmesg`|`sudo dmesg -T \| grep 'custom_kernel'`|Rootkit or unusual kernel logs during boot|
|`auth.log`|`grep 'Accepted password' /var/log/auth.log`|Successful SSH logins, potential compromise|
|`auth.log`|`grep 'sudo' /var/log/auth.log`|Commands run with elevated privileges|
|`syslog`|`grep 'CRON' /var/log/syslog`|Suspicious or unauthorized cron jobs|
|`wtmp`|`last -f /var/log/wtmp`|Full login history by user, IP, and session duration|
|`btmp`|`lastb` or `utmpdump /var/log/btmp`|All failed login attempts with timestamps and sources|

#journal-Commands
## Journal Commands Table
| Argument     | Description                                                | Example                               |
| ------------ | ---------------------------------------------------------- | ------------------------------------- |
| `-f`         | Follow the journal and show new entries as they are added. | `journalctl -f`                       |
| `-k`         | Show only kernel messages.                                 | `journalctl -k`                       |
| `-b`         | Show messages from a specific boot.                        | `journalctl -b -1`                    |
| `-u`         | Filter messages by a specific unit.                        | `journalctl -u apache.service`        |
| `-p`         | Filter messages by priority.                               | `journalctl -p err`                   |
| `-S`         | Show messages since a specific time.                       | `journalctl -S "2021-05-24 14:08:01"` |
| `-U`         | Show messages until a specific time.                       | `journalctl -U "2021-05-24 15:46:01"` |
| `-r`         | Reverse the output, showing the newest entries first.      | `journalctl -r`                       |
| `-n`         | Limit the number of shown lines.                           | `journalctl -n 20`                    |
| `--no-pager` | Do not pipe the output into a pager.                       | `journalctl --no-pager`               |
#auditd
## 🛡️ Linux `auditd` Overview (All-in-One Table)
|**Category**|**Detail / Command**|**Description / Purpose**|
|---|---|---|
|**What is auditd?**|Linux Audit Daemon|Collects and writes security logs in user-space|
|**Log File**|`/var/log/audit/audit.log`|Stores all audit event logs|
|**Persistent Rules File**|`/etc/audit/audit.rules`|Stores audit rules permanently|
|**Temporary Rule Utility**|`auditctl`|Used to set temporary audit rules|
|**Search Logs**|`ausearch`|Searches audit logs based on keys, time, PID, etc.|
|**Generate Report**|`aureport`|Converts ausearch output to a human-readable summary|
|**Real-Time Monitoring**|`audispd`|Dispatches logs to SIEMs or triggers actions in real time|
|**Monitor /etc/passwd**|`sudo auditctl -w /etc/passwd -p wra -k users`|Watch file for write, read, and attribute changes, tagged with `users`|
|**Monitor execve Calls**|`sudo auditctl -a always,exit -F arch=b64 -S execve -k execve_syscalls`|Monitors execution of commands via `execve` syscall|
|**Search `/etc/passwd` access**|`sudo ausearch -k users`|Shows logs with key `users`|
|**Search execve calls**|`sudo ausearch -k execve_syscalls`|Shows logs with key `execve_syscalls`|
|**Hex-decoded Search**|`sudo ausearch -i -k serverdir-changes`|Decodes hex values (e.g., proctitle) for better readability|
|**Report Example**|`sudo ausearch -k users \| aureport -f user-logs`|Generates readable report from logs tagged with `users`|
|**Typical Event Data**|`comm="vim" exe="/usr/bin/vim.basic"`|Shows executable name used during the event|
|**Event Context Info**|`cwd="/home/ubuntu", tty=pts1, uid=0, pid=6402, subj=unconfined`|Captures user ID, terminal, working dir, process ID, and security context|
## Linux Logs Capstone
```bash
grep "404" /var/log/apache2/access.log* 
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:02:04 +0000] "GET /favicon.ico HTTP/1.1" 404 493 "http://10.10.133.134:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:30:45 +0000] "POST /upload.php HTTP/1.1" 404 494 "http://10.10.133.134:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:33:14 +0000] "GET /cmd.php HTTP/1.1" 404 494 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
/var/log/apache2/access.log.1:10.10.133.134 - - [23/Jun/2024:21:33:41 +0000] "GET /favicon.ico HTTP/1.1" 404 493 "http://10.10.133.134:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:122.0) Gecko/20100101 Firefox/122.0"
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:34:11 +0000] "POST /upload.php HTTP/1.1" 404 494 "http://10.10.133.134:8080/" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
```

## `Grep` Command to Identify the Reverse shell port
```bash 
 grep "cmd.php" /var/log/apache2/access.log*
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:33:14 +0000] "GET /cmd.php HTTP/1.1" 404 494 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:41:02 +0000] "GET /cmd.php?ip=10.10.190.69&port=5000 HTTP/1.1" 200 203 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:21:44:56 +0000] "GET /cmd.php?ip=10.10.190.69&port=5000 HTTP/1.1" 200 203 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
/var/log/apache2/access.log.1:10.10.190.69 - - [23/Jun/2024:22:01:31 +0000] "GET /cmd.php?ip=10.10.190.69&port=5000 HTTP/1.1" 200 203 "-" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0"
```

## Command to verify the useradd
```bash
sudo journalctl -u tests.service | grep "useradd"
Jun 23 22:03:25 tryhackme sudo[16521]:     root : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/usr/sbin/useradd attacker
Jun 23 22:03:25 tryhackme useradd[16522]: new group: name=attacker, GID=1001
Jun 23 22:03:25 tryhackme useradd[16522]: new user: name=attacker, UID=1001, GID=1001, home=/home/attacker, shell=/bin/sh, from=none
```


## Q/A
```Q/A 
Q1 Which type of logs provide messages related to hardware events and system errors?
A1 Kernel
Q2 What is the memory space used to store system messages?
A2 Kernel ring buffer
Q3 What is the default log level used to inform about non-imminent errors?
A3 WARNING
Q4 Which log file can be used to record failed login attempts only?
A4 btmp
Q5 What severity level keyword is used to indicate immediate action is needed in a syslog message?
A5 alert
Q6 What facility code is used for cron jobs?
A6 9
Q7 To configure the persistence of journal logs, which parameter has to be modified within the journald configuration file?
A7 Storage
Q8 Which utility is used to search for auditd logs?
A8 ausearch
Q9 What command can be used to search logs related to a session opened for a user?
A9 sudo grep -i "session opened" /var/log/auth.log
Q10 What is the IP address from which the application was exploited?
A10 10.10.190.69
Q11 What file contains the reverse shell?
A11 cmd.php
Q12 At which port was the reverse shell running?
A12 5000
Q13 What is the file name that was being executed with sudo privileges?
A13 tests.sh
Q14 What is the name of the user created using the service?
A14 attacker
Q15 Was the new account ever logged in to? y/n
A15 n
```