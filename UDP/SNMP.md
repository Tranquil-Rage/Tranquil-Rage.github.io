---
layout: default
title: SNMP
parent: UDP
nav_order: 3-2
---
# SNMP | 161
- It is common to see SNMP misconfigured, lots of enumeration can be done through this
- Versions 1 & 2 are unencrypted - sniff this with wireshark
- Version 3 is encrypted
- Often default community strings are used (and known)
- Dump user accounts for further password spraying

## Identify with Nmap
```
nmap -sV -p 161 --script=snmp-info $IP-SUBNET
```

nmap scripts to extract values from targets (MIB tree included in scripts)
- /usr/share/nmap/scripts/snmp`*`
Example brute force using SNMP community strings:
```
sudo nmap -sU $IP -p161 -script=snmp-brute.nse
```

## Dump User Accounts using snmpwalk
```
snmpwalk -c public -v1 $TARGET 1.3.6.1.4.1.77.1.2.25
```


#### MIB Tree
The MIB tree values are fixed:
<p>
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
</p>

Look For:
- Domain names
- Machine names
- Usernames
- Software Versions (antivirus?)



#### Tools - Versions 1 & 2
<b>onesixtyone</b>
- Make a file called 'community' consisting of 'public, private, manager' and run onesixtyone -c community $IP
```
onesixtyone -c names -i hosts
```

<b>snmpwalk</b>
- Must provide -c (community string), -v (version -1,2 or 3), -t (timeout, e.g. 10) and $IP
```
snmpwalk -c public -v1 $IP | grep hrSWRunName
```

<b>snmpenum</b>
```
snmpenum -t $IP
```

#### Tools - Version 3
<b>snmpwalk wrapper</b>
```
apt-get install snmp snmp-mibs-download
wget https://raw.githubusercontent.com/raesene/TestingScripts/master/snmpv3enum.rb
```

[[NOTE]]
{: .note }
Metasploit wordlists for SNMP v1 & v2 credentials (/usr/share/metasploit-framework/data/wordlists/snmp_default_pass.txt), SecLists for a more up to date list
