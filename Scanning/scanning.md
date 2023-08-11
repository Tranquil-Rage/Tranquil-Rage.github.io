---
title: Scanning
layout: default
---
# Scanning a Target
## nmap
Nmap scripts located under: /usr/share/nmap/scripts/

Examples:
```
nmap -sC -sV -p- $IP --open
```

]
##  Common UDP Services for finding misconfigurations:
### SNMP (See Services)
- Versions 1 & 2 are unencrypted - sniff this with wireshark
- Version 3 is encrypted
- Often default community strings are used (and known)

nmap scripts to extract values from targets (MIB tree included in scripts)
- /usr/share/nmap/scripts/snmp`*`
Example brute force using SNMP community strings:
```
sudo nmap -sU $IP -p161 -script=snmp-brute.nse
```
#### MIB Tree
The MIB tree values are fixed:
| Fixed Values           | Information to Extract       |
|:-----------------------|:-----------------------------|
| 1.3.6.1.2.1.25.1.6.0   | System Process               |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs             |
| 1.3.6.1.2.1.25.4.2.1.4 | Process Path                 |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units                |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name                |
| 1.3.6.1.4.1.77.1.2.25  | User Accounts                |
| 1.3.6.1.2.1.6.13.1.3   | TCP Local Ports              |


Look For:
- Domain names
- Machine names
- Usernames
- Software Versions (antivirus?)
##### Tools
onesixtyone
- Make a file called 'community' consisting of 'public, private, manager' and run onesixtyone -c community $IP

snmpwalk
- Must provide -c (community string), -v (version -1,2 or 3), -t (timeout, e.g. 10) and $IP


### DNS
If misconfigured - extract all sub-domains and their IP addresses by using Zone Transfer

##### Ping:
- A ttl of 63 means the box is linux


----

[^1]: [It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site).

[Just the Docs]: https://just-the-docs.github.io/just-the-docs/
[GitHub Pages]: https://docs.github.com/en/pages
[README]: https://github.com/just-the-docs/just-the-docs-template/blob/main/README.md
[Jekyll]: https://jekyllrb.com
[GitHub Pages / Actions workflow]: https://github.blog/changelog/2022-07-27-github-pages-custom-github-actions-workflows-beta/
[use this template]: https://github.com/just-the-docs/just-the-docs-template/generate
