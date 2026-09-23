# Networking 

```Description 
_Flags have been omitted from this writeup per platform guidelines._

An intruder has breached the internal OT network and systematically probed industrial devices for sensitive data. Network captures reveal unusual traffic from a suspicious host scanning PLC memory over TCP port 502.

Analyse the provided PCAP and uncover what data the attacker retrieved during their register scans. with an attachment 
```

Open `Wireshark` 
```bash 
wireshark <file.pcap>
```
then Add Filters 
```Filters
tcp.port == 502
```
and I Got Output 
```Output
|No.|Time (s)|Source IP|Dest IP|Protocol|Info|
|---|---|---|---|---|---|
|1|0.000000|`10.10.145.25`|`10.10.75.165`|TCP|SYN — Source (34005) → Destination (502)|
|2|0.000037|`10.10.75.165`|`10.10.145.25`|TCP|SYN, ACK — Destination (502) → Source (34005)|
|3|0.000232|`10.10.145.25`|`10.10.75.165`|TCP|ACK — Connection established|
|4|0.000561|`10.10.145.25`|`10.10.75.165`|Modbus/TCP|**Query** — Function Code `0x03`: Read Holding Registers|
|5|0.000576|`10.10.75.165`|`10.10.145.25`|TCP|ACK — Acknowledging Modbus request|
```

After Analyzing, Add new filter
```Filter 
modbus.func_code == 3 && ip.src == 10.10.75.165
```
Then 
- **Right-click a response packet → Follow TCP Stream** to isolate that transaction.
And got the flag in the stream 
Flag
```Flag 
[flag omitted]
```