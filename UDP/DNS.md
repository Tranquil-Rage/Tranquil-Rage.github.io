---
layout: default
title: DNS
parent: UDP
nav_order: 3-1
---
# DNS | 53
- If misconfigured - extract all sub-domains and their IP addresses by using Zone Transfer

<br />
#### Windows Zone Transfer
```
nslookup
set type=any
ls -d example.com
```

<br />
#### Linux Zone Transfer
```
dig axfr example.com @ns1.example.com
OR
dig AXFR -p 53 domain-name.local @10.10.15.140
```

