---
layout: default
title: SMB
parent: TCP
nav_order: 4-3
---
# SMB / CIFS / Samba (NetBios) | 445
## Methodology
<ul>
<li>  Check for SMB Null sessions </li>
	<ul>
	<li> ```smbclient -L $IP``` OR ```smbclient //'192.168.1.204'/sharename -N```</li>
	<li> Note: <b>enum4linux</b> - if the server permits null sessions, it will iterate through SIDs and gather <b>system level users</b>) ```enum4linux $IP ``` </li>
	</ul>
<li> Enumerate shares with <b>nmap</b> </li>
	<ul>
	<li> ```nmap -p139,445 --script=smb-enum-shares $IP ```</li>
	</ul>
<li> Enumerate the domain with <b>enum4linux</b> ```enum4linux $IP```</li>
<li> We know a share exists but have no access?</li>
	<ul>
	<li> crackmapexec to brute force it: ```crackmapexec smb $IP -u Username -p PasswordOrFileList```</li>
	<li> msf module: ```use auxiliary/scanner/smb/smb_login```</li>
	</ul>
<li> Can we put files in a public Resources? Can that resource be access say through a web interface? We can upload a shell and obtain code execution through a misconfiguration of these shares perhaps. </li>
<li> Have a valid user list? Test a password with various users using <b>Smbmap</b> ```smbmap -u user -p 'password' -H $IP```</li>
	<ul>
	<li> If we can log in, secured folders may become read/write access - can we then do the above? </li>
	</ul>
<li> Always check for <b>password reuse</b> for multiple users on Windows machines </li>
<li> Head over to <a href="../Windows">Windows Exploitation</a> which includes Impacket-LookupSIDs for SMB</li>
</ul>

## Tools
- smbclient: ``` smbclient //'192.168.1.204'/share -U'qiu'%'password'
- crackmapexec
- enum4linux: ``` enum4linux $IP ```
- smbmap
- rpcclient
- nbtscan

#### Command Reference
<b> Smbclient </b>
- Download everything from a share that may be locked to a specific user, given the password (specify the password sign %)
```smbclient //$IP/secured -U user%password -c "prompt OFF; recurse ON; mget *"```
> - The -L command lets you look at what services are available on the server
> - The -I command is useful if your netbios names don't match the TCP/IP hostnames (or trying to reach a host on another network)

###### [REFERENCES]
