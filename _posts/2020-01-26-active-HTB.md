---
title:  "Hack The Box / Active"
date: 2020-01-26
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/active/active-header.png"
---
Active is our fourth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was a great learning experience where SMB enumeration and some knowledge about [kerberos](https://en.wikipedia.org/wiki/Kerberos_(protocol))  were essential in order to root this machine. This is the fourth blog before my third attempt to the OSCP exam, so let's get to it!

## Information Gathering

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.59
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/nmap.png" alt="nmap scan">
