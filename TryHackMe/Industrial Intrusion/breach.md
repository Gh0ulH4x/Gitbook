# WarmUp
_Flags have been omitted from this writeup per platform guidelines._

lets first start, we got two IP-Address one with AttackBox and Second one is Target Machine 
```IP_Addresss
Attacker machine!
Status:Connected via AttackboxYour machine IP Address:10.10.255.114
Target machine!
Status:OnTitle:breach v3Target IP Address:10.10.200.185
```
then we just start the rust scan on the Attack-Machine 
```bash 
rustscan -a 10.10.200.185
PORT      STATE SERVICE      REASON
22/tcp    open  ssh          syn-ack
80/tcp    open  http         syn-ack
102/tcp   open  iso-tsap     syn-ack
502/tcp   open  mbap         syn-ack
1880/tcp  open  vsat-control syn-ack
8080/tcp  open  http-proxy   syn-ack
44818/tcp open  EtherNetIP-2 syn-ack
```
Then, Visiting `http://10.10.200.185` displayed a **static webpage of a locked gate** secured by a **badge-based access system**.
```Hint 
At this point, no interactivity was possible. This hinted at backend logic controlling whether the gate is shown as **locked** or **unlocked**.
```
so then we start covering other ports and find out that
These ports are typically found in **Industrial Control Systems (ICS)**:
- `102/tcp` → **ISO-TSAP** (Siemens S7comm for PLCs)
- `502/tcp` → **Modbus/TCP** (SCADA/PLC comm)
- `44818/tcp` → **EtherNet/IP** (Allen-Bradley Rockwell PLCs)

Then we discovered about the second port which is allowed and that is 
Accessing `http://10.10.200.185:1880` revealed a **Node-RED interface**:
- `/flow` → The Node-RED **editor** (a visual programming environment)
- `/ui` → A **dashboard** for interacting with UI elements
```Node-Red
Node-RED is a **flow-based development tool for visual programming** used heavily in IoT and ICS environments. It allows control logic to be built using drag-and-drop nodes connected in a flow.
Here, it was controlling the access mechanisms of the gate**, with toggles for:
- **Motion Detector**
- **Badge Scanner**
Each toggle had a corresponding flow node that interacted with the backend logic controlling the gate status.
```
##Attempted Login on Port 8080 – OpenPLC Interface
- `http://10.10.200.185:8080` revealed the **OpenPLC web server login page**.
- **Default creds (admin:admin)** didn’t work.    
- This suggested the OpenPLC was secured (good practice), so we moved back to Node-RED for further exploitation.
And After investigating the port 1880/ui 

🛠️Exploitation via Node-RED Dashboard
```Visiting `/ui` showed:
Two toggle switches labeled:
- ✅ Motion Detector
- ✅ Badge 
  
Exploit Path:
```STEPS
1. Disabled both toggles.
2. Revisited `http://10.10.200.185` on Port 80.
3. ✅ The gate was now open.
4. 🎯 Captured the flag from the now-accessible page.
```

🔬Behind the Operation:
```Server-Side_Flow
Port 80 (Web UI) checks internal variables or states (like `motion_enabled` and `badge_enabled`).
These states are controlled by **Node-RED flows** which:
Read from user input on `/ui`
Send commands to backend logic (maybe OpenPLC or internal script)
Disabling both mechanisms via Node-RED updates the logic flow.
Gate state changes → UI updates with access granted.
```

That's the Flag visible on Port 80 Website with Gate Open:
```Flag 
[flag omitted]
```