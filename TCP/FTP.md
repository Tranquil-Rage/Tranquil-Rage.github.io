---
layout: default
title: FTP
parent: TCP
nav_order: 4-1
---
# FTP
## Methodology
- Check for anonymous login 
```
ftp $IP 21
anonymous : anonymous
```
- If anonymous login doesn't work, move on or try for a brute-force in the background

#### Command Reference
- Download: get $file
- Upload: put $file

#### Brute-Force
- Hydra
``` 
hydra -l username -P /usr/share/worlists/rockyou.txt -f $IP ftp -V
```

