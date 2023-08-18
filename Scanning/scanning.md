---
title: Scanning
layout: default
nav_order: 2
---


# Port Scans
## nmap
Nmap scripts located under: /usr/share/nmap/scripts/

Examples:
<b> Sample port scan using simple scripts & service versions </b>
```
nmap -sC -sV -p- $IP --open
```
<b> TCP Syn 'stealth' scan - do not complete the three-way handshake </b>
```
nmap -sS $IP
```
<b> Ping scan with OS detection and scripts on the nmap top ports </b>
```
sudo nmap -Pn -sS -sC -sV -v -oN top-ports.nmap $IP
```

## nikto
Basic vulnerability scanning services
```
nikto -host $IP
```

## [INFORMATION]
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
