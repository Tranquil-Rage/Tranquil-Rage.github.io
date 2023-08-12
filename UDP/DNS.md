---
layout: default
title: DNS
parent: UDP
nav_order: 1
---
### DNS
If misconfigured - extract all sub-domains and their IP addresses by using Zone Transfer

#### Windows
Windows DNS Zone Transer:
```
nslookup
> set type=any
ls -d example.com
```

#### Linux
```
dig axfr example.com @ns1.example.com
```

