---
layout: default
title: SNMP
parent: UDP
nav_order: 2
---
# SNMP | 161
- Versions 1 & 2 are unencrypted - sniff this with wireshark
- Version 3 is encrypted
- Often default community strings are used (and known)

nmap scripts to extract values from targets (MIB tree included in scripts)
- /usr/share/nmap/scripts/snmp`*`
Example brute force using SNMP community strings:
```
sudo nmap -sU $IP -p161 -script=snmp-brute.nse
```
#### MIB Tree
The MIB tree values are fixed:
| Fixed Values           | Information to Extract       |
|:-----------------------|:-----------------------------|
| 1.3.6.1.2.1.25.1.6.0   | System Process               |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs             |
| 1.3.6.1.2.1.25.4.2.1.4 | Process Path                 |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units                |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name                |
| 1.3.6.1.4.1.77.1.2.25  | User Accounts                |
| 1.3.6.1.2.1.6.13.1.3   | TCP Local Ports              |
...

Look For:
- Domain names
- Machine names
- Usernames
- Software Versions (antivirus?)

##### Tools
###### onesixtyone
- Make a file called 'community' consisting of 'public, private, manager' and run onesixtyone -c community $IP
###### snmpwalk
- Must provide -c (community string), -v (version -1,2 or 3), -t (timeout, e.g. 10) and $IP
