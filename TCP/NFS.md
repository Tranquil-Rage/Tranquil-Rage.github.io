---
layout: default
title: NFS 
parent: TCP
nav_order: 4-3
---
# NFS | 2049
## Methodology
- If <b>no_root_squash</b> is <b>enabled</b> it allows any user to upload the sticky bit (SUID binaries) on the machine - in theory when you upload a binary with the SUID bit to the target machine, the permissions are transferred and preserved (so upload a shell as the root user)
- If <b>no_all_squash</b> is <b>enabled</b> similar to the above but it applies to non-root users, you can still have the shell as a nobody user and use it to emulate a non-root user. Create a suid file as that user and execute the suid as the nobody user to become a different user)
- Mount shares with: ```sudo mount $IP:/share /home/local/mountLocation
- Nmap scripts can be used to enumerate shares: ```--script=nfs-ls,nfs-statfs,nfs-showmount```
- NFS may be connected to rpcbind accessible on another port

### Command Reference


### Tools
### Attacks
