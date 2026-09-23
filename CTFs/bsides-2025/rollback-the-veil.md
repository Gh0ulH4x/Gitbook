# Rollback the Veil

## Description
```Description
# Rollback the Veil

Final challenge room of BSides 2025
```
----
```Network 
Web - 10.10.150.10
DC1 - 10.10.150.121
SKELETON - 10.10.150.150
SERVER1 - 10.10.150.101
SERVER2 - 10.10.150.102

Attacker - 192.168.101.2
```
---
#### Route
```bash
$ ip route
default via 192.168.40.2 dev eth0 proto dhcp src 192.168.40.128 metric 100
10.10.150.0/24 via 192.168.101.1 dev lateralmovement metric 1000
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
172.18.0.0/16 dev br-6247455b4f3c proto kernel scope link src 172.18.0.1 linkdown
192.168.40.0/24 dev eth0 proto kernel scope link src 192.168.40.128 metric 100
192.168.101.0/24 dev lateralmovement proto kernel scope link src 192.168.101.2
```
----
#### Enumeration
```bash
fping -agq 10.10.150.0/24
10.10.150.1
10.10.150.10
10.10.150.250
```
- Network Ping 
```bash
nmap -A 10.10.150.250
Open 10.10.150.250:22
Open 10.10.150.250:1194
Open 10.10.150.250:1337

nmap -A 10.10.150.10
Open 10.10.150.10:22
Open 10.10.150.10:80
```
